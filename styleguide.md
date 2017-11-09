# Android style guide

 - [Package structure](#package-structure)
 - [Syntax](#syntax)
 - [Naming](#naming)
 - [Constants](#constants)
 - [If statements](#if-statements)
 - [Error handling](#error-handling)
 - [Kotlin](#kotlin)

## Package structure

*Note all lower case package names*

 - dk.company.appname
    - base
    - domain
        - api
        - injection
        - interactors
            - login
              - LoginInteractor.java
              - LogoutInteractor.java
            - splash
              - InitSplashInteractor.java
        - models
        - translation
            - Translation.java
    - ui
        - shared
            - LogoutDialog.java
        - login
            - LoginFragment.java
            - LoginMvpView.java  <- Or LoginContract.java?
            - LoginPresenter.java
        - signup
        - splash
        - main
             - MainActivity.java
        - App.java

## Class structure

### Fields

 - Order your fields and group them together and have all the fields at the top of the class.
 - Try to have some logic around the order of your methods. Group relevant stuff together, avoid having random view util methods in the middle of a MVP view interface.

**Do**

```java
class SomethingFragment implements SomethingMvpView {
    
    @BindView(R.id.id)
    TextView nameTv;
    
    @BindView(R.id.id)
    TextView passwordEt;
    
    @Inject
    LoginPresenter presenter;
    
    private boolean someFlag;
    private boolean otherFlag;
    
    void onCreate(...) {
    ...
    }
    
    void someMethod() {
    ...
    }
    
    @Override
    void showLoading() {
    ...
    }
    
    @Override
    void showContent() {
    ...
    }
}
```

**Don't**
```java
class SomethingFragment {
    
    @BindView(R.id.id)
    TextView nameTv;
    
    @Inject
    LoginPresenter presenter;
    
    private boolean someFlag;
    
    @BindView(R.id.id)
    TextView passwordEt;
    
    void onCreate(...) {
    ...
    }
    
    @Override
    void showContent() {
    ...
    }
    
    private boolean otherFlag;
   
    void someMethod() {
    ...
    }
    
    @Override
    void showContent() {
    ...
    }
}
```

## Syntax

### Paranthesis & Brackets

```java
// Always use brackets
if (something) {
    return;
}

// Not allowed
if (something) return;

// Not allowed
if (something)
    return;
```

### Comments

**Don't**
```java
//something imporant

/*somthing important*/
```

**Do**
```java
// Something important

/*
Something important
*/
```


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

```Java
Retrofit retrofit = new Retrofit.Builder()
    .baseUrl(BuildConfig.API_URL)
    ...
    .client(client)
    .build();

    return retrofit.create(BackendService.class);
```

#### Constants that doesn't change

*Don't*: Use one giant `Constants.java`

*Do*: 
```java
public class IntentKeys {
    public static final String LOGIN_USER = "LOGIN_USER";
    ...
}

```

# If statements

**Minimize logic in ifs**

Try to boil down `if` statements. If you have many expressions, consider changing them to boolean local variables:

```java
// Hard to read
if(repo.get(id).getAge() < repo.get(otherId).getAge() && otherRepo.getItems().contains(id) && SomethingManager.getSomeState() == SomeState.X) {
    ...
}

// Better
boolean isYounger = repo.get(id).getAge() < repo.get(otherId).getAge();
boolean existsInList = otherRepo.getItems().contains(id);
boolean isStateX = SomethingManager.getSomeState() == SomeState.X;
if(isYounger && existsInList && isStateX) {
    ...
}
```

**Avoid nested ifs**

Following logic in a deeply nested if else mess can be hard and error prone for the next developer. There are several ways of avoiding that.

Consider inverting the expression and returning:
```java
// Hard to follow
if (item != null) {
   if (item.getFollowers() != null && item.getFollowers().get(someId) != null) {
       follower = item.getFollowers().get(someId);
   }
}

// Better
if(item == null) {
    return;
}

if(item.getFollowers() == null || !item.getFollowers().contains(someId)) {
    return;
}

follower = item.getFollowers().get(someId);
```

# Error handling

## Api responses

## Try catch

# Kotlin

## When

## Let 

## Apply
