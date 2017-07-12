# Bitrise Setup

## Make an app on Bitrise
If you haven't been invited to bitrise.io, please contact your tech lead or manager.

1) Make a new app on: [bitrise.io](https://www.bitrise.io/dashboard)
2) Make sure that "App new app to" points to Nodes.
3) Select the repo
4) Click auto-add SSH key
5) Validation setup: Make sure *master* is selected and click next. This will take a min or two
6) Project build configuration: Should detect Android, click next & confirm
7) Webhook setup: Click "register a webhook for me". This enabled github to notify bitrise, that something happened when you push
8) Abort the first build it kicks off

## Setup workflow
1) Go to the first dashboard by clicking on Dashboard > *app name*
2) Open "Workflow Editor" & open "Secrets"
3) Make two keys `HOCKEY_TOKEN` and `SLACK_WEBHOOK_URL`. Copy the values from one of the other projects.
4) *OPTIONAL* Go to "Env Vars" and add `PROJECT_SLACK_CHANNEL` in *App Environment Variables*. Set it to the project channel on slack. It will post to #bitrise if you dont.
5) Go to *bitrise.yml* and paste in this gist: https://gist.github.com/johsoe/b294b791adeda5c6792b79ad9920c8e3

## How to kick off a build
There's two ways to kick off a build with the above gist:
1) Push a tag in [SEMVER](http://semver.org/) format, i.e. `1.0.0`
2) Manually open Bitrise and start a build. (Can be necessary in case something goes wrong, or whatever)

With the tags, we get a nice overview of all the different builds and have an easier way of doing roll backs on the master branch.

# Gradle plugin

The gradle plugin in short filters and builds whatever flavor / buildtype mix you want. After having completed each build, it outputs the build info into a shared JSON file on the CI server. 

The JSON format is in the form:
```json
[{
    "build": "app-staging.apk",
    "hockeyId": "yourKeyShouldGoHere",
    "appId": "com.example.app.staging",
    "mappingFile": "null"
}]
```

This is later used for the Hockey deployment utils for uploading apks for each different hockey app.

## build.gradle setup

Add the dependency to the (Project) `build.gradle`. Check the [the latest version here](https://github.com/nodes-android/ci-bitrise-gradle-plugin/blob/master/build.gradle#L19)
```groovy
buildscript {
    // ...
    dependencies {
        // ...
        classpath 'dk.nodes.ci:bitrise:0.92'
    }
}
```

Setup and apply the plugin in (App) `build.gradle`
```groovy
apply plugin: 'dk.nodes.ci.bitrise'
```

Add Hockey Id's and what buildtype you want to deploy for each flavor:
```groovy
hockeyAppId = "11111111111111111111"
deploy = "debug"
```

_*OPTIONAL.*_ You can setup defaults in the `bitrise` block. This is useful if you have many flavors and only want to build some of them.
```groovy
bitrise {
    // Our default deployment if none is specified in the flavor config
    defaultDeployMode = "release|staging"
    // What flavor should we deploy
    flavorFilter = "firstSkin" 
}
```

## Flavor examples

### Classic projects
So in this example, we have a *staging* and *production* flavor pointing to two different endpoints and having two different applicationId's.

We also have two different buildtypes, the debug version and the release version, which is proguarded.
 
 The current options are then to build:
  - stagingDebug
  - productionRelease
  
```groovy
productFlavors {
    staging {
        // bitrise gradle options
        hockeyAppId = "11111111111111111111"
        deploy = "debug"
        
        applicationIdSuffix ".staging"
        signingConfig signingConfigs.staging
        buildConfigField "String", "API_URL", "\"https://project.staging-like.st/\""
        buildConfigField "String", "HOCKEY_ID", "\"" + hockeyAppId + "\""
        manifestPlaceholders = [
            HOCKEYAPP_APP_ID: hockeyAppId,
            AUTOBACKUP      : false
        ]
    }
    production {
        // bitrise gradle options
        hockeyAppId = "11111111111111111111"
        deploy = "release"
        
        signingConfig signingConfigs.release
        buildConfigField "String", "API_URL", "\"https://project.like.st/\""
        buildConfigField "String", "HOCKEY_ID", "\"" + hockeyAppIdId + "\""
        manifestPlaceholders = [
            HOCKEYAPP_APP_ID: hockeyAppId,
            AUTOBACKUP      : false
        ]
    }
}

buildTypes {
        debug {
            
        }
        release {
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            proguardFiles fileTree('proguard').asList().toArray()
        }
    }
```
