# Android project guidelines

### Package naming

com._clientname_._appname_ /
dk._clientname_._appname_


### NStack setup

TODO


### 


### Commonly used thirdparty libs
#### New Relic
This horrible piece of software uses an extra evil brand of reflection to look at your apps HTTP communication, intercepting errors and uploading them to some horrible cloud interface Toby looks at error now and then. This should be added when we're dealing with an externally developed API. For evidence gathering.

ProTip: Use BuildConfig.DEBUG to turn it off on debug builds because its debug output is infuriatingly annoying and it stackthraces all over the place.

