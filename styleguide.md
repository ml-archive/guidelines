# Android style guide


### Package structure

_WIP_

 - dk.company.appname
    - base
    - common
        - api
        - injection
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

### Constants

#### Environment keys/constants

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