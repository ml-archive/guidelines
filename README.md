# Android project guidelines

### Package naming

com._clientname_._appname_ /
dk._clientname_._appname_


### NStack setup

TODO

### Patterns
#### MVP
Way of structuring business logic, data storage and presentation (UI) in a way that makes a lot of sense on larger projects. USE IT. Examples:

- http://git.ournodes.com/android/mvp_rx_sample Basic app template using MVP

### 


### Commonly used thirdparty libs
#### New Relic
This horrible piece of software uses an extra evil brand of reflection to look at your apps HTTP communication, intercepting errors and uploading them to some horrible cloud interface Toby looks at error now and then. This should be added when we're dealing with an externally developed API. For evidence gathering.

ProTip: Use BuildConfig.DEBUG to turn it off on debug builds because its debug output is infuriatingly annoying and it stackthraces all over the place.

#### HockeyApp integration
Are you feeling hockey? punk!?. We ALWAYS integrate hockey (even though its kindda lame). Remember to differentiate your initialization based on the BuildConfig.DEBUG flag. In debug mode you want the crash submission dialog as well as the update check. On release builds on the other hand you want silently uploaded (sneaky) crash reports and no update checking. (yo, use nstack b) 

