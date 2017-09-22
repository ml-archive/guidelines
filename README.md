# Android project guidelines

### Package naming

com._clientname_._appname_ /
dk._clientname_._appname_

### Keystore

 1. Create a keystore in AS with an alias name.
 2. Upload/Find keystores to Google Drive here: https://drive.google.com/open?id=0B09IfosUwe8iSXVxUWR6am9wSmc 
 3. Create a doc with the passwords/aliases

### NStack setup
See [NSTACK](https://github.com/nodes-android/nstack)

We have a rewritten smaller version of nstack in Kotlin [https://github.com/nodes-android/kstack](https://github.com/nodes-android/kstack). Going forward this is the version of nstack we'll improve and work on.

### Patterns
#### MVP
Way of structuring business logic, data storage and presentation (UI) in a way that makes a lot of sense on larger projects. It is mandatory.

It is important to understand how repositories work and what is their role in a project. Failing to understand this will end up with huge presenters taking all of the burden.

Interactors are a plus, should be considered on large projects.

#### Clean architecture
On the bigger enterprise projects, most of the clean arch patterns help provide modularity and provide a better base for unit testing.
 - Interactors are a must for small isolated pieces of work. Have them do one task only and test that.
 - Every model should have a Repository for CRUD'ing over disk/network
 - Keep Presenters as small as possible and try to make then be the connection between interactors and the view.

### Stack

Check out our blog for a quick run through of our [internal stack](https://engineering.nodesagency.com/our-stacks/android/)

[**NStack SDK**](https://github.com/nodes-android/NStack) Offers dynamic localisation for our apps and other useful functionalities. If you go to [nstack.io](https://nstack.io/) and select your project and translate, you will be able to add new sections with new Translations. These translations can be changed in NStack and the changes will reflect in the app without the need of an app update.

[**okhttputils**](https://github.com/nodes-android/okhttputils) Collection of often used utilities for okhttp or related to communicating with a backend over HTTP. This contain logging interceptors for error logging as well as a generic error handler and retrofit helpers.

[**filepicker**](https://github.com/nodes-android/filepicker) As the title goes it is a library for picking a file and getting a URI back. Also deals with special and very complicated case of obtaining and image from the camera on Android.
Does not yet support SAF, but its in the pipeline.

[**nutils**](https://github.com/nodes-android/nutils) nutils contains often used smaller pieces of code which can't be anywhere else.

[**gutenberg**](https://github.com/nodes-android/gutenberg) Our font loading and setting library. If your app uses custom fonts, you need this.

### Commonly used thirdparty libs
#### New Relic
This piece of software uses an brand of reflection to look at your apps HTTP communication, intercepting errors and uploading them to a cloud interface. This should be added when we're dealing with an externally developed API.

ProTip: Use BuildConfig.DEBUG to turn it off on debug builds because its debug output is infuriatingly annoying and it stack traces all over the place.

#### HockeyApp integration
We always integrate hockey. Remember to differentiate your initialization based on the BuildConfig.DEBUG flag. In debug mode you want the crash submission dialog as well as the update check. On release builds on the other hand you want silently uploaded (sneaky) crash reports and no update checking. 

### Proguard integration
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

## Upload checklist

 - Analytics is tracking. Verify on Google Analytics website
 - Hockey is correctly added (Tracking crash reports, version alerts should be disabled by the Debug flag)
 - Install on top of old cached app if it is an update to check its behaviour (QA should already have checked this)
 - Confirm version number with PM
 - Check that you are referring to the LIVE version of the Facebook App, not the DEV version.
 - Does the app handle missing internet connection properly?
 - Does the app handle a slow internet connection properly?
 - Double check views are not clipping on small phones. (Add scrollviews or use weights where needed)
 - Check Map views with release key
 - Make sure all social-media keys are correct(SHA-1 ect)
 - App version name / code correctly set (http://semver.org/)
 - Check for libraries included multiple times
 - Check production build of app for any proguard errors
 - Make sure you run the APK you ship at least once.
 - Upload keystore file to Google drive / Include password (https://drive.google.com/open?id=0B09IfosUwe8iSXVxUWR6am9wSmc) 
 
 
# Team meetings
Here are the recurring meetings the Android team takes part in:

#### Android Team Meeting
This is the main meeting in the Android team, where we talk about the latest things happening in our team. General direction of the team, brief general team performance review, what people need help with, amount of workload, should we use or not _that_ framework, etc. It happens every month.

#### Production meetings

The production meetings is a bi-monthly meeting where all our developers and QA take part. Each meeting, another team has the agenda, and they talk about something relevant to their domain, but which can affect all of us. We try to maximize the knowledge sharing so we can all become better together. 

#### Quarterly Company Catch-up

This is a quarterly meeting, where the board tells all the employees how the company did in the last quarter and what our targets and short, mid and long-term plans are. You'll find out if the sales and production are on track, below or ahead of target, and anything else relevant to the company. It's not only a presentation, feel free to ask questions. 

#### Ask for other Meetings
Depending if you are in London, Copenhagen or Aarhus you will have other meetings. Make sure you are updated on the different ones that haven;t been included here.


# Your First Day
- Start by installing Android Studio and the different tools that we use, see Tools we use
- Ask [Johnny](https://nodes.slack.com/messages/@joso) to invite you to [our private git](https://github.com/nodes-projects/). He will add you to the correct groups. Here is where we have our client projects.
- Ask [Johnny](https://nodes.slack.com/messages/@joso) to invite you to [our GitHub organisation](https://github.com/nodes-android) so you can contribute to build great open source libraries. 
- Ask [Johnny](https://nodes.slack.com/messages/@joso) to invite you to Hockey
- Ask [Casper](https://nodes.slack.com/messages/@cr) for access to Postman
- Have a project manager add you to our Trello organisation
- Ask [Jonas](https://nodes.slack.com/messages/@josc) to add you to Slack and Ask [Johnny](https://nodes.slack.com/messages/@joso) to add you to the correct channels.
- Make sure you read the [Nodes Handbook](https://docs.google.com/document/d/1E4ZyGqIKDttGlJ0c0rEkNy2lxP-n1PBwQzuaESbLnpw/edit) (private document). 
- We have Facebook groups for internal announcements. Ask [Johnny](https://nodes.slack.com/messages/@joso) to add you to the groups.
- Ask where the coffee machine is


# Tools We Use
[**Slack**](https://nodes.slack.com) Is our main communication tool. We use this for daily communication. You can message any Nodes user directly or you can join group chats called channels.

[**Trello**](https://trello.com) Is our main project management tool.  Each project has its own Trello board. Make sure Trello is updated when starting and finishing tasks. This will help the whole team to have a better overview of the project. Ask your project manager if you have questions.

[**Harvest**](https://nodes.harvestapp.com/) is the time tracking tool that we use. Make sure to always harvest on the appropriate project. Ask the PM on which Harvest project you should track the time.

[**Forecast**](https://forecastapp.com/) is the planning tool that we use. Check Forecast when you get to work to have an overview of what you're assigned to for that day. If there are any changes the PM will let you know. If you finish earlier than expected and you are still allocated please tell your PM.

[**Postman**](https://www.getpostman.com/) is a great tool which helps you see and test web APIs. Our backend team uses Postman to test and document their APIs. Every project has its own library which can contain multiple folders with different endpoints.

[**Zeplin**](https://zeplin.io/) Is the tool we use to check the Android designs of each of our projects. In Zeplin, you can see each screen in the design, get info about the sizes, margins, paddings, fonts, colours and also export assets for our app. Try to use SVG's if possible and feel free to have multiples of 8 to follow the Android design guidelines.

[**Hockey**](https://www.hockeyapp.net/) Is our tool for in-house distribution and crash reporting.

[**NStack**](https://nstack.io/) is a service we built and it is the tool we use for doing localisation. Together with the [**NStack SDK**](https://github.com/nodes-android/NStack) it offers dynamic localisation for our apps and other usefull functionalities. If you go to [nstack.io](https://nstack.io/) and select your project and translate, you will be able to add new sections with new Translations. These translations can be changed in NStack and the changes will reflect in the app without the need of an app update.

# Coding Guidelines
Make sure to read our [**Android Style Guide**](https://github.com/nodes-android/guidelines/blob/master/styleguide.md).  


# Nodes CI
Our CI system is now hosted by Bitrise. We have several tools and scripts used for building and deploying.

 - [Gradle build plugin](https://github.com/nodes-android/ci-bitrise-gradle-plugin)
 - [Deploy bitrise step (ruby)](https://github.com/nodes-android/ci-bitrise-deploy-step)
 - [Changelog step (gathering commit messages for a changelog)](https://github.com/nodes-android/ci-bitrise-changelog-step)
 
 We have a [guide for setting up the CI](https://github.com/nodes-android/guidelines/blob/master/Bitrise.md)


# Working at Nodes
Having one office in London, one in Copenhagen and one in Aarhus means you might get to work with colleagues who are not in the same office as you are. If that is the case, make sure to rely as much as possible on Trello and the project's channel on Slack.

It helps a lot if you try to foresee any problems you might run into and let the PM know. This is to avoid the situation where you need an answer from someone in order to continue with your work. It is fine to take the lead and tackle design problems for example. Just make sure to let your PM know about the problem and the solution since the PM's must be always kept in the loop.

Estimate sprints before starting any ticket. If before starting a ticket you feel that the estimate has changed, re-estimate and let your PM know. Try to harvest per ticket copy-pasting the link of the ticket you are working on so the PM can have a clear overview of how the time is being used.

If you need something (an image asset, some input from the PM, etc), make a Trello card asking for it. If it is urgent you can ping them on Slack.

If there's some feedback from the client but you're not sure what to do about it ask your PM and explain the different options with an estimate of each one. Feel free to advise for the best solution but make sure you explain why.

If in doubt, have a talk to discuss things. Try to write down notes and conclusions of the meeting and share it with your project's channel if its a problem that affects other platforms.
