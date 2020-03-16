# Android style guide

 - [Package structure](#package-structure)
 - [Syntax](#syntax)
 - [Naming](#naming)
 - [Constants](#constants)
 - [If statements](#if-statements)
 - [Error handling](#error-handling)
 - [Kotlin](#kotlin)


## Modules And Packages Structure


Following clean architecture guidelines our projects are split into three separate modules: Domain, Data, Presentation and App
> Note all packages are in lower case


### App module package structure
App module is the main application module. It must contain only your `Application` class and `Dagger` files to provide dependencies injection for the whole application

- dk.company.appname.app
  - initlizers
    - AppInitlizer.kt
  - injection
    - components
    - modules
      - AppModule.kt
      - InteractorModule.kt
      - RestModule.kt
      - RepositoryModule.kt
  - App.kt


### Presentation module package structure
Presentation module contains all ui-related logic: Fragments, ViewModels, Custom views and etc.
 - dk.company.appname.presentations
    - base
    - nstack
      - Translation.java
      - RateReminderActions.kt
    - extensions
      - ContextExtensions.kt
      - ViewExtensions.kt
    - ui
        - shared
            - AppDialog.kt
        - login
            - LoginFragment.kt
            - LoginViewModel.kt
            - LoginViewState.kt
        - signup
        - splash
        - main
             - MainActivity.kt
             - MainViewModel.kt
             - MainViewState.kt
    - util


### Domain Module Package Structure
Domain module contains Repository interfaces, Interactors and Entities that used by them
- dk.company.appname.domain
    - extensions
    - managers
    - repository
      - UserRepository.kt
      - PostsRepository.kt
    - interactors
      - Interactor.kt
      - GetPostsInteractor.kt
    - models
      - User.kt
      - Post.kt

### Data Module Package Structure
Data module contains repository implementations and local/remote data sources
- dk.company.appname.data
  - repository
    - UserRepositoryImpl.kt
    - PostsRepositoryImpl.kt
  - database
  - network

## Activity/Fragment Class structure

### Fields

 - Order your fields and group them together and have all the fields at the top of the class.
 - Try to have some logic around the order of your methods
 - When subscribing for a  `LiveData`'s state try splitting rendering of these properties into separate functions


```kotlin
class SomethingFragment {

  private val viewModel: SomethingViewModel by viewModel()
  private val args: SomethingFragmentArgs by navArgs()

  override fun onActivityCreated(savedInstanceState: Bundle?) {
    viewModel.viewState.observeNonNull(this) { state ->
         showLoading(state)
         showPosts(state)
         showErrorMessage(state)
     }
 }

 private fun showPosts(state: SampleViewState) {
   // Show Posts
 }

 private fun showLoading(state: SampleViewState) {
   // Show Loading state}

 private fun showErrorMessage(state: SampleViewState) {
    // Handle error dialog
  }
}
```

## Syntax

### Paranthesis & Brackets
The curly braces around a body can be omitted if the body is a oneliner. This is discouraged unless the body goes on the same line as the condition



<table width="100%">
<tr>
    <th>Do</th>
    <th>Don't</th>
  </tr>
<tr>
<td>

```kotlin
// OK
if (something) doSomething()
```

```kotlin
// OK
if (something) {
  doSomething()
}
```


</td>
<td>

```kotlin
// Not allowed
if (something)
  doSomething()
```


</td>
</tr>
</table>


### Comments


<table width="100%">
<tr>
    <th>Do</th>
    <th>Don't</th>
  </tr>
<tr>
<td>

```kotlin
// Something important
val x = y ?: default
```

</td>
<td>

```kotlin
//something important
val x = y ?: default
```

</td>
</tr>
<tr>
<td>

```kotlin
/*
  Something important
*/
fun someComplexMethod() {
  ...
}
```


</td>
<td>


```kotlin
/*somthing important*/
fun someComplexMethod() {
  ...
}
```


</td>
</tr>

</table>


## Naming

### XML

*View Ids*

After Kotlin introduced the extensions that autogenerates view bindings, naming conventions of xml elements is even more important.

The format is _layout_ _name_ _viewtype_ ~ `loginPasswordEt` or put another way, think of it as nstack with section-Key-ViewType

**File names**

- list_item_name *Adapter views for listviews/recyclerviews*
 - fragment_name *Fragment views*
 - include_name  *Include layouts, try to add some context here as well i.e. include_main_toolbar or include_list_item_something_header*
 - activity_name  *Activity views*
 - dialog_name *Dialogs*



### Classes

 - Repositories can either be named `SomethingRepo` or `SomethingRepository`
 - Interactors are in the format `SomethingInteractor`
 - Activities/Fragments are named `SomethingFragment` / `SomethingActivity`
 - Views/Layouts are `SomethingView` / `SomethingLayout`

## Constants

### Environment keys/constants

Keys or constants that change depending on environment / flavor should be put in the `build.gradle` file.

That means you can use `BuildConfig.KEY` in your code instead of i.e. `Constants.KEY`. The build system can then change to the correct set of keys depending on what environment you build towards.

Example:
```groovy
buildConfigField "String", "API_URL", "\"https://test.site.dk/api/\""
buildConfigField "String", "HOCKEY_ID", "\"123412341234\""
buildConfigField "int", "SOME_ENV_SPECIFIC_ID", "1"
buildConfigField "boolean", "API_ENV_VAR", "false"
```

```kotlin
Retrofit retrofit = Retrofit.Builder()
    .baseUrl(BuildConfig.API_URL)
    ...
    .client(client)
    .build()

    return retrofit.create(BackendService.class)
```

#### Constants that doesn't change

<table width="100%">
<tr>
    <th>Do</th>
    <th>Don't</th>
  </tr>
<tr>
<td>

```kotlin
object ArgumentKeys {
  const val LOGIN_USER = "LOGIN_USER"
}
object DefaultValues {
  const val ITEM_CNT = 30
}

```

</td>
<td>

Use one giant `Constants.kt`
```kotlin
 object Constants {
   const val DEFAULT_ITEM_CNT = 30
   const val ARG_ITEM_ID = "itemId"
   ...
 }
```

</td>
</tr>

</table>



## Null Safety


# If statements

### Minimize logic in ifs

Try to boil down `if` statements. If you have many expressions, consider changing them to boolean local variables:

<table width="100%">
<tr>
    <th>Do</th>
    <th>Don't</th>
  </tr>
<tr>
<td>

```kotlin
// Better
val isYounger = repo.get(id).getAge() < repo.get(otherId).getAge()
val existsInList = otherRepo.getItems().contains(id)
val isStateX = SomethingManager.getSomeState() == SomeState.X

if(isYounger && existsInList && isStateX) {
    ...
}
```

</td>
<td>

```kotlin
// Hard to read
if(repo.get(id).getAge() < repo.get(otherId).getAge()
 && otherRepo.getItems().contains(id)
 && SomethingManager.getSomeState() == SomeState.X) {
    ...
}
```

</td>
</tr>

</table>


## if vs. when

Use `if` for binary checks. When there are more then two options consider using `when` instead

<table width="100%">
<tr>
    <th>Do</th>
    <th>Don't</th>
  </tr>
<tr>
<td>

  ```kotlin
  if(condtion) {
    doSomething()
  } else {
    doSomethingElse()
  }
  ```

</td>
<td>

  ```kotlin
  when(condtion) {
    true -> doSomething()
    false -> doSomethingElse()
  }
  ```

</td>
</tr>
<tr>
<td>

```kotlin
when (size) {
  1 -> doSomething()
  in 1..10 -> doIfConditionB()
  else -> doSomethingElse()
}
```

</td>
<td>



  ```kotlin
  if (size == 1) {
    doSomething()
  } else if (size <= 10 && size > 1) {
     doIfConditionB()
  } else {
    doSomethingElse()
  }
  ```

</td>
</tr>
</table>

### Avoid nested ifs

Following logic in a deeply nested if else mess can be hard and error prone for the next developer. There are several ways of avoiding that.

Consider inverting the expression and returning:
```kotlin
// Hard to follow
if (item != null) {
   if (item.getFollowers() != null && item.getFollowers().get(someId) != null) {
       follower = item.getFollowers().get(someId)
   }
}

// Better
if(item == null) {
    return
}

if(item.getFollowers() == null || !item.getFollowers().contains(someId)) {
    return
}

follower = item.getFollowers().get(someId)
```
