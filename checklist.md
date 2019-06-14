# New build checklist

 - QA team verifies most, but do them a favor and check that Google Maps/Push etc is working when making a build after changing stuff.
 - Check that Hockey/Firebase keys are correctly setup to each flavor in `build.gradle`
 - Make release notes with relevant commit messages/trello/jira tickets
 - Confirm version number with PM
 - Check production build of app for any proguard errors
 
# When going live
 
 - Merge all PR's and merge develop <-> master, all that git flow jazz
 - Upload keystore file to team drive.
 - Make sure you run the APK you ship at least once. 
 - When doing major releases, test out your new build on top of the current live build. There can be data migration issues, is user data persisted etc etc.

# Proguard integration
We use proguard in our release builds to get rid of unused code and obfuscate code. There's a proguard template for the most common libraries we use on the Nodes Template project (https://github.com/nodes-projects/template-project-android).

There is a proguard folder in the root of the project **(make sure you update .gitignore if adding this setup to an existing project as this folder was being ignored in previous versions)** that contains a rules file for each of the libraries we use. Just delete the ones you don't need.

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

shrinkResources gets rid of the reference to unused resources.

If you find any issues with proguard in any project regarding a specific library just check on that library website for up to date proguard rules and just update the file. **Please also update the file in the template project**
