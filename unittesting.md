# Unit testing

## Mocking

Mockito has some shortcommings when it comes to Kotlin. There is [`mockito-kotlin`](https://github.com/nhaarman/mockito-kotlin) library which makes mockito a little bit better with Kotlin. It is a DSL built around mockito for making mockito somewhat better with kotlin. [`mockk`](https://github.com/mockk/mockk) seems to be a better choice for Kotlin.

### Creating Mocks

```kotlin
class SomeTest {

    @MockK
    lateinit var someClassMock: SomeClass
    @MockK
    lateinit var someOtherClassMock: SomeOtherClass

    @Before
    fun setup() {
        MockKAnnotations.init(this, relaxUnitFun = ture)
    }
}

```

Mocking without annotations

```kotlin
class SomeTest {
    lateinit var someClassMock: SomeClass
    lateinit var otherClassMock: OtherClass

    @Before
    fun setup() {
        someClassMock = mockk(relaxUnitFun = true)
        otherClassMock = mockk(relaxUnitFun = true)
    }
}
```

With JUnit test class is created for every test method in the class. If `SomeTest` contains 4 `@Test` annotated methods then `SomeTest` will be created before executing each of them. Therefore it's not necessary to initialize fields in `@Before`.

```kotlin
class SomeTest {
    private val someClassMock = mockk<SomeClass>(relaxUnitFun = true)
    private val otherClassMock = mockk<OtherClass>(relaxUnitFun = true)
}
```

It is good to keep in mind a [few differencies](https://stackoverflow.com/questions/6094081/junit-using-constructor-instead-of-before) between initializing tests on construction and inside `@Before`. In almost all cases the differencies are not important.

### Relax

Mockito doesn't require to specify return values for mocked methods. If code calls a mocked method without a specifed returned value then mockito will return `null` in case of objects or it will do nothing in case of `void`. `Mockk` throws `MockKException: no anser found for [SOME_CALL]` when code calls a function without a specified returned value (answer). In order not to specify an answer for every call a `relax` flag can be set.

```kotlin
private val otherClassMock = mockk<OtherClass>(relaxUnitFun = true) // this will make Unit functions return Unit
private val someClassMock = mockk<SomeClass>(relaxed = true) // this will provide a simple value for every unspecified call
```

### Answering calls

```kotlin
every { someClassMock.someFunction() } returns Something()
every { someClassMock.otherFunction(3) } returns SomethingElse()
every { someClassMock.otherFunction(any()) } returns SomethingElse()
every { someClassMock.thirdFunction("String", any()) } returns 8
every { someClassMock.aFunction(match { /* Do some check on the value */ }) } returns "one string"
every { someClassMock.aFunction(match { /* Some other check */ }) } returns "another string"
every { someClassMock.aProperty } returns AProperty()
```

`every` doesn't record generic types.

```
every { someClassMock.someFunction<AType>() } returns 4
every { someClassMock.someFunction<BType>() } returns 5
```

the code will disregard the differenceis in the generic type, therefore this

```
val result = someClassMock.someFunction<AType>()
println("result is $result")
```

will print `result is 5`

### Verifying calls

```kotlin
    verify { someClassMock.someFunction() }
    verify { someClassMock.anotherFunction(3) }
    verify { someClassMock.thirdFunction(any()) }
    verify {
        someClassMock.thirdFunction(match {
            // Do some check on the value thirdFunction was called with
        })
    }

    verify {
        someClassMock.someFunction()
        someClassMock.anotherFunction(1)
    }
```

## Concurrent code

`verify` functions have `timeout` parameter for verifying concurrent code.

```kotlin
@Test
fun `Test async`() {
    testTarget.firstFunction()

    verify(timeout = 400L) { firstMock.firstFunction() }
}
```

If `testTarget.firstFunction` starts an async operation then `verify` will wait for at most 400 milliseconds for `firstMock.firstFunction` to get called. If it's called earlier then the test instantly completes successfully. If it's not called after 400 milliseconds then the test fails.

When testing concurrent code with timeout a successful run of all tests once does not guarantee that they pass every time. There is an option to run tests until failure in unit tests configuration.

It is more reliable to make concurrent code execute sequentially.

### Coroutines

Mockk provides functions for answering and verifying `suspend` calls

```kotlin
coEvery { someMock.suspendFunction() } returns true
...
coVerify { someMock.suspendFunction() }
 ```

When suspend functions are being tested it is enough to use `runBlocking`.

```kotlin

class SomeClass {
    ...
    suspend fun suspendFunction(): Something {
        ...
    }
}

@Test
fun `Test something`() {
    val result = runBlocking { someClass.suspendFunction() }

    assert(/* something about result */)
}
```

#### Presenters

When using coroutines in presenters use `launchOnIO`, `launchOnUI`, `asyncOnIO`, `asyncOnUI` functions from `BasePresenterImpl`. To make async code execute sequentially add `runBlockingTest` to test sources

```kotlin
@InternalCoroutinesApi
fun <V: AppBaseView, P: AppBasePresenter<V>> runBlockingTest(presenter: P, block: suspend P.() -> Unit) {
    return runBlocking(TestContext()) {
        presenter.activateTestMode(this.coroutineContext)
        return@runBlocking block.invoke(presenter)
    }
}
```

and make calls to presenter functions with it

```kotlin
@UseExperimental(InternalCoroutinesApi::class)
class SomePresenterTest {
    ...
    @Test
    fun `Test something`() {
        runBlockingTest(somePresenter) { someFunction() }

        // assert and verify results
    }
}
```

### RxJava

Rx schedulers can be injected into classes that use them and then changed to `trampoline` when testing.

```kotlin
interface RxSchedulers {
    val io: Scheduler
    val main: Scheduler
    ...
}

class RxSchedulersImpl : RxSchedulers {
    override val io = Schedulers.io()
    override val main = AndroidSchedulers.mainThread()
    ...
}

class SomeClass @Inject constructor(
    ...
    private val rxSchedulers: RxSchedulers
) {
    ...
    observerSomething()
        .subscribeOn(rxSchedulers.io)
        .observeOn(rxSchedulers.main)
    ...
}

class MockSchedulers : RxSchedulers {
    override val io = Schedulers.trampoline()
    override val main = Schedulers.trampoline()
    ...
}

class SomeTest {

    private val someClass = SomeClass(..., MockSchedulers())
}
```

There are some problems with this approach. It requires every class which uses rx for concurrency to be injected with schedulers provider. And some of the rx operators choose schedulers on their own. It's better to prepare schedulers for testing globally. It can be done with a rule.

```kotlin
class RxSchedulersRule : TestRule {

    override fun apply(base: Statement, d: Description): Statement {
        return object : Statement() {
            @Throws(Throwable::class)
            override fun evaluate() {
                RxJavaPlugins.setIoSchedulerHandler { Schedulers.trampoline() }
                RxJavaPlugins.setComputationSchedulerHandler { Schedulers.trampoline() }
                RxJavaPlugins.setNewThreadSchedulerHandler { Schedulers.trampoline() }
                RxAndroidPlugins.setMainThreadSchedulerHandler { Schedulers.single() }

                try {
                    base.evaluate()
                } finally {
                    RxJavaPlugins.reset()
                    RxAndroidPlugins.reset()
                }
            }
        }
    }
}

class SomeTest {
    @Rule
    @JvmField var testSchedulerRule = RxSchedulerRule()

    init {
        // any initialization which uses rx schedulers has to be moved to @Before, because the rule is not applied yet
    }

    @Before
    fun setup() {
        // do initialization here
    }
}
```

Is seems easier to just use an utility function

```kotlin
fun mockRxSchedulers() {
    RxJavaPlugins.setIoSchedulerHandler { Schedulers.trampoline() }
    RxJavaPlugins.setComputationSchedulerHandler { Schedulers.trampoline() }
    RxJavaPlugins.setNewThreadSchedulerHandler { Schedulers.trampoline() }
    RxAndroidPlugins.setMainThreadSchedulerHandler { Schedulers.trampoline() }
}

class SomeTest {

    init {
        mockRxSchedulers()

        // now we don't need @Before
    }
}
```

## Mock Data

It is a good idea to create an object for mock data and get data objects from there

```kotlin
data class InnerModel(val i: Int)

data class Model(
    val i: Int,
    val s: String,
    val m: InnerModel
)

object TestMockData {
    
    fun createModel(): Model { // it's better to create a model every time
        return Model(3, "string", createInnerModel())
    }

    val model by lazy { // better not to do that, because if model classes have "var" fields then some tests may modify the fields and make other tests fail
        Model(1, "s", createInnerModel())
    }

    fun createInnerModel(): InnerModel {
        return InnerModel(9)
    }
}

class FirstTest {

    private val model = TestMockData.createModel()

    @Test
    fun `Test something`() {
        ...
    }
}

class SecondTest {

    @Test
    fun `Test 1`() {
        // it is easier to make methods for creating models without arguments and then set the fields with "copy"
        val model = TestMockData.createModel().copy(i = 8, s = "another string")
    }

    @Test
    fun `Test 2`() {
        val innerModel = TestMockData.innerModel()
    }
}
```

## Testing interactor

```kotlin

interface BaseInputAsyncInteractor<T, I> {

    suspend fun run(input: I): T
}

interface SomeInteractor<SomeInteractor.Result, SomeInteractor.Input> {

    data class Input(val s: String)

    sealed class Result {
        object Success : Result()
        object Failure: Result()
    }
}

class SomeInteractorImpl @Inject constructor(
    private val dependency: Dependency
) : SomeInteractor {

    override suspend fun run(input: SomeInteractor.Input): SomeInteractor.Result {
        return if (dependency.doSomething(input.s)) {
            SomeInteractor.Result.Success
        } else {
            SomeInteractor.Result.Failure
        }
    }
}

class SomeInteractorTest {

    private val dependencyMock = mockk<Dependency>()

    private val someInteractor = SomeInteractor(dependencyMock)

    @Test
    fun `Test successfully doing something with dependency returns success`() {
        val s = "string"

        every { dependency.doSomething(s) } returns true

        val result = runBlocking { someInteractor.run(SomeInteractor.Input(s)) }

        assert(result is SomeInteractor.Result.Success)
    }

    @Test
    fun `Test failing to do something with dependency returns failure`() {
        val s = "string"

        every { dependcy.doSomething(s) } returns false

        val result = runBlocking { someInteractor.run(SomeInteractor.Input(s)) }

        assert(result is SomeInteractor.Result.Failure)
    }
}

```

## Testing presenter

```kotlin
interface ScreenContract {
    
    interface View : BaseView {
        fun showSuccess()
        fun showError()
    }

    interface Presenter : BasePresenter {
        fun onSomeButtonClick()
    }
}

class ScreenPresenter @Inject constructor(
    private val someInteractor: SomeInteractor
) : BasePresenterImpl<ScreenContract.View>, ScreenContract.Presenter {

    override fun onViewCreated(view: ScreenContract.View, lifecycle: Lifecycle) {
        super.onViewCreated(view, lifecycle)
        getSomething()
    }

    override fun onSomeButtonClick() {
        getSomething()
    }

    private fun getSomething() {
        launchOnUI {
            val result = asyncOnIO { someInteractor.run(SomeInteractor.Input("string")) }.await()
            if (result is SomeInteractor.Result.Success) {
                view { showSuccess() }
            } else {
                view { showError() }
            }
        }
    }
}

@UseExperimental(InternalCoroutinesApi::class)
class ScreenPresenterTest {
    
    private val someInteractorMock = mockk<SomeInteractor>()
    
    private val viewMock = mockk<ScreenContract.View>(relaxUnitFun = true)
    private val lifecycleMock = mockk<Lifecycle>(relaxUnitFun = true)

    private val presenter = ScreenPresenter(someInteractorMock)

    @Test
    fun `Test success is shown when something is loaded successfully`() {
        coEvery { someInteractorMock.run(any()) } returns SomeInteractor.Result.Success

        runBlockingTest { onViewCreated(viewMock, lifecycleMock) }

        verify { viewMock.showSuccess() }
    }

    @Test
    fun `Test error is shown when failing to load something`() {
        coEvery { someInteractorMock.run(any()) } returns SomeInteractor.Result.Failure

        runBlockingTest { onViewCreated(viewMock, lifecycleMock) }

        verify { viewMock.showFailure() }
    }

    @Test
    fun `Test something is loaded when clicking on a button`() {
        coEvery { someInteractorMock.run(any()) } returns SomeInteractor.Result.Failure

        runBlockingTest { onSomeButtonClick() }

        coVerify { someInteractorMock.run(any()) }
    }
}

```

### Arguments

It's better not to do anything with the arguments in the view. Pass arguments to the presenter, this way it becomes possible to test presenter with different arguments

```kotlin

interface AppBaseView : BaseView {
    val extras: Bundle?
}

interface SomeScreenContract {
    interface View : AppBaseView { ... }
    ...
}

class SomePresenter @Inject constructor(
    private val firstInteractor: FirstInteractor,
    private val secondInteractor: SecondInteractor
) {
    ...
    override fun onViewCreated(view: ScreenContract.View, lifecylce: Lifecycle) {
        ...
        if (view.extras?.getBoolean("FLAG", false) == true) {
            doOneThing()
        } else {
            doOtherThing()
        }
    }

    private fun doOneThing() {
        launchOnIO { firstInteractor.run() }
    }

    private fun doOtherThing() {
        launchOnIO { secondInteractor.run() }
    }
}

class SomePresenterTest {

    private val viewMock = mockk<SomeScreenContract.View>(relaxUnitFun = true)
    private val bundleMock = mockk<Bundle>()

    init {
        every { viewMock.extras } returns bundleMock
    }

    ...

    @Test
    fun `Test first interactor is executed when flag is true`() {
        every { bundleMock.getBoolean("FLAG", any()) } returns true

        runBlockingTest(presenter) { onViewCreated(viewMock, lifecycleMock) }

        coVerify { firstInteractor.run() }
    }

    @Test
    fun `Test second interactor is executed when flag is false`() {
        every { bundleMock.getBoolean("FLAG", any()) } returns false

        runBlockingTest(presenter) { onViewCreated(viewMock, lifecycleMock) }

        coVerify { secondInteractor.run() }
    }
}
```

`Bundle` cannot be created and used normally, it has to be mocked. This will not work

```kotlin
val bundle = Bundle().apply { putBoolean("FLAG", true) }
every { viewMock.extras } returns bundle
```

## Api calls

```kotlin
open class MockCall<T>(private val data: T? = null) : Call<T> {

    protected var executed = false

    private var cancelled = false

    override fun enqueue(callback: Callback<T>) {
        val response = Response.success(200, data)
        callback.onResponse(this, response)
    }

    override fun isExecuted(): Boolean {
        return executed
    }

    override fun clone(): Call<T> {
        return this
    }

    override fun isCanceled(): Boolean {
        return cancelled
    }

    override fun cancel() {
        cancelled = true
    }

    override fun execute(): Response<T> {
        executed = true
        return Response.success(200, data)
    }

    override fun request(): Request {
        return Request.Builder().build()
    }
}

class UnsuccessfulMockCall<T>(
        private val errorBodyString: String? = null
) : MockCall<T>() {

    override fun enqueue(callback: Callback<T>) {
        val response = Response.error<T>(404, ErrorResponseBody(errorBodyString))
        callback.onResponse(this, response)
    }

    override fun execute(): Response<T> {
        executed = true
        return Response.error(404, ErrorResponseBody(errorBodyString))
    }
}

...
    every { apiMock.first() } returns MockCall()
    every { apiMock.second() } returns MockCall(TestMockData.createModel())
    every { apiMock.third() } returns UnsuccessfulMockCall()
    every { apiMock.fourth() } returns UnsuccessfulMockCall("{code: \"12345\"}")
...
```
