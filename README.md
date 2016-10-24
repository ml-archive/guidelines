# Android project guidelines

### Package naming

com._clientname_._appname_ /
dk._clientname_._appname_

### Keystore

 1. Create a keystore in AS with an alias name.
 2. Upload/Find keystores to Google Drive here: https://drive.google.com/open?id=0B09IfosUwe8iSXVxUWR6am9wSmc 
 3. Create a doc with the passwords/aliases

### NStack setup

TODO

### Patterns
#### MVP
Way of structuring business logic, data storage and presentation (UI) in a way that makes a lot of sense on larger projects. USE IT. Examples:

- http://git.ournodes.com/android/mvp_rx_sample Basic app template using MVP

### Anti Patterns (Do not do or use) - X, Alternative and Reason
- Support action bar. Toolbar. The system handled action bars causes a lot of problems when you wan't to show and hide the bar.
- Whole Screen inside a Recyclerview. Coordinator layout. Harder to read and to mantain.
- Hardcode colors. Make a resource colour instead and a style if needed. Easier when creating flavour or app reskin.
- Listviews. Use Recyclerviews instead. Better performance and readability.
- Relativelayout. Use Framelayout and/or Linearlayout instead (if possible). Better performance and readability.

### Commonly used thirdparty libs
#### New Relic
This horrible piece of software uses an extra evil brand of reflection to look at your apps HTTP communication, intercepting errors and uploading them to some horrible cloud interface Toby looks at error now and then. This should be added when we're dealing with an externally developed API. For evidence gathering.

ProTip: Use BuildConfig.DEBUG to turn it off on debug builds because its debug output is infuriatingly annoying and it stackthraces all over the place.

#### HockeyApp integration
Are you feeling hockey? punk!?. We ALWAYS integrate hockey (even though its kindda lame). Remember to differentiate your initialization based on the BuildConfig.DEBUG flag. In debug mode you want the crash submission dialog as well as the update check. On release builds on the other hand you want silently uploaded (sneaky) crash reports and no update checking. (yo, use nstack b) 

#### Proguard integration
We need to use proguard in our release builds to get rid of unused code and obfuscate the rest of it so you can't reverse engineer the apk. There's a proguard template for the most common libraries we use on the Nodes Template project (https://git.nodescloud.com/android/templateproject).

Basically there's a proguard folder in the root of the project **(make sure you update .gitignore if adding this setup to an existing project as this folder was being ignored in previous versions)** that contains a rules file for each of the libraries we use. Just delete the ones you don't need.

In the gradle file we need this setup:
~~~gradle
buildTypes {
      release {
          minifyEnabled true
          shrinkResources true
          proguardFiles getDefaultProguardFile('proguard-android.txt')
          proguardFiles fileTree('proguard').asList().toArray()
      }
  }
~~~

We should also use the shrinkResources that basically gets rid of resources that the proguard script identified that were not being used. The proguard alone just deletes references for the resources on the generated files not deleting them specifically.

If you find any issues with proguard in any project regarding a specific library just check on that library website for up to date proguard rules and just update the file. **Please also update the file in the template project**

## Upload checklist

 - Analytics is tracking
 - Hockey is correctly added (Tracking crash reports, version alerts disabled)
 - Install on top of old app - if update
 - Confirm version number with PM
 - Check that you are referring to the LIVE version of the Facebook App, not the DEV version.
 - Does the app handle missing internet connection properly?
 - Does the app handle a slow internet connection properly?
 - Double check views are not clipping on small phones. (Add scrollviews where needed)
 - App.Debug = false
 - Check Map views with release key
 - Make sure all social-media keys are correct(SHA-1 ect)
 - App version name / code correctly set (http://semver.org/)
 - Check for libraries included multiple times
 - Make sure you run the APK you ship at least once.
 - Upload keystore file to Google drive / Include password (https://drive.google.com/open?id=0B09IfosUwe8iSXVxUWR6am9wSmc)
