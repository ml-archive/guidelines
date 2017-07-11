# Gradle


## Flavors

### Classic projects

```
defaultConfig {
    applicationId "dk.client.project"
    minSdkVersion 17
    targetSdkVersion 25
        
    versionCode 62
    versionName "5.0.4"
}

productFlavors {
    staging {
        def hockeyId = "11111111111111111111"
        applicationIdSuffix ".staging"
        signingConfig signingConfigs.staging
        buildConfigField "String", "API_URL", "\"https://project.staging-like.st/\""
        buildConfigField "String", "HOCKEY_ID", "\"" + hockeyId + "\""
        manifestPlaceholders = [
            HOCKEYAPP_APP_ID: hockeyId,
            AUTOBACKUP      : false
        ]
    }
    production {
        def hockeyId = "11111111111111111111"
        signingConfig signingConfigs.staging
        buildConfigField "String", "API_URL", "\"https://project.like.st/\""
        buildConfigField "String", "HOCKEY_ID", "\"" + hockeyId + "\""
        manifestPlaceholders = [
            HOCKEYAPP_APP_ID: hockeyId,
            AUTOBACKUP      : false
        ]
    }
}
```

### "Reskin" projects - WIP

```
defaultConfig {
    
    minSdkVersion 17
    targetSdkVersion 25
        
    versionCode 62
    versionName "5.0.4"
}

productFlavors {
    firstSkin {
        def hockeyId = "11111111111111111111"
        applicationId "dk.client.firstskin"
        signingConfig signingConfigs.staging
        buildConfigField "String", "API_URL", "\"https://project.staging-like.st/\""
        buildConfigField "String", "HOCKEY_ID", "\"" + hockeyId + "\""
        manifestPlaceholders = [
            HOCKEYAPP_APP_ID: hockeyId,
            AUTOBACKUP      : false
        ]
    }
    secondSkin {
        applicationId "dk.client.secondskin"
        def hockeyId = "11111111111111111111"
        signingConfig signingConfigs.staging
        buildConfigField "String", "API_URL", "\"https://project.like.st/\""
        buildConfigField "String", "HOCKEY_ID", "\"" + hockeyId + "\""
        manifestPlaceholders = [
            HOCKEYAPP_APP_ID: hockeyId,
            AUTOBACKUP      : false
        ]
    }
}

buildTypes {
        debug {
            signingConfig null
        }

        release {
            minifyEnabled true
            shrinkResources true
            proguardFiles getDefaultProguardFile('proguard-android.txt')
            proguardFiles fileTree('proguard').asList().toArray()
        }
    }
```