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

### Avoid Anti Patterns
- Support action bar. Please don't use it. The system handled action bars causes a lot of problems when you wan't
to show and hide the bar. Just use a regular goddamn toolbar and deal with few extra lines of setup code.
- Do not implement entire screens worth of UI inside a RecyclerView.Adapter :P
- Max 2 or 3 indentation levels per function, avoid arrow code, make more functions.
- Isolate frequently repeated blocks of code into a function.


### Commonly used thirdparty libs
#### New Relic
This horrible piece of software uses an extra evil brand of reflection to look at your apps HTTP communication, intercepting errors and uploading them to some horrible cloud interface Toby looks at error now and then. This should be added when we're dealing with an externally developed API. For evidence gathering.

ProTip: Use BuildConfig.DEBUG to turn it off on debug builds because its debug output is infuriatingly annoying and it stackthraces all over the place.

#### HockeyApp integration
Are you feeling hockey? punk!?. We ALWAYS integrate hockey (even though its kindda lame). Remember to differentiate your initialization based on the BuildConfig.DEBUG flag. In debug mode you want the crash submission dialog as well as the update check. On release builds on the other hand you want silently uploaded (sneaky) crash reports and no update checking. (yo, use nstack b) 

## Upload checklist

 - Analytics is tracking
 - Hockey is correctly added (Tracking crash reports, version alerts disabled)
 - Install on top of old app - if update
 - Confirm version number with PM
 - Check that you are referring to the LIVE version of the Facebook App, not the DEV version.
 - Does the app handle missing internet connection properly?
 - Does the app handle a slow internet connection properly?
 - Double check views are not clipping on small phones. (Add scrollviews where needed)
 - App.Debug = false (in never apps use BuildConfig.DEBUG and made sure you don't import the BuildConfig from an included proect, this way it will automatically get set)
 - Check Map views with release key
 - Make sure all social-media keys are correct(SHA-1 ect)
 - App version name / code correctly set (http://semver.org/)
 - Check for libraries included multiple times
 - Make sure you run the APK you ship at least once.
 - Upload keystore file to Google drive / Include password (https://drive.google.com/open?id=0B09IfosUwe8iSXVxUWR6am9wSmc)
