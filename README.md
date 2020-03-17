# How we work

## Kotlin
All new projects in Nodes are developed in Kotlin and use coroutines extensively. As many others we are constantly embracing the new features in Kotlin and using them as fast as we can.

## Template project
Given our project churn in Nodes, it's important to have a good start to a project, so we created a template with our stack already set up + CI ready Gradle configurations.

Our [newest template](https://github.com/nodes-android/kotlin-template) is built on a MVVM/clean architecture/multi module approach following the current trends and best practices. Before MVVM we used a MVP base depending on an [arch library](https://github.com/nodes-android/nodes-architecture-android) shaping how we use interactors and presenters. The arch library is still being maintained and supported.

## Patterns
### MVVM
Our newest template for client projects use [Google's ViewModels](https://developer.android.com/topic/libraries/architecture/viewmodel) with a lightweight ViewState approach, similar to MVI.

We believe this is a much needed "standardization" of the current Android development practices that will help onboarding of new developers, but also handover to clients and general collaboration.

### MVP
Before the switch to MVVM we used a MVP template depending on extending base presenters from our arch library.

**Presenters**:
Our presenters attach to the view's lifecycle so detachment and clean up is automatically handled - similar to the many others MVP solutions out there.

Since the switch to Kotlin, our presenters also expose Coroutine scopes and helper extension methods to ease the verbosity of Dispatchers/Contexts.

**View**:
We use a standard "Contract" approach with interfaces for the Presenter and View in the same class.

### Clean architecture
 - Interactors are a must for small isolated pieces of work. Have them do one task only and test that.
 - Every model should have a Repository for CRUD'ing over disk/network
 - Keep Presenters/ViewModels as small as possible and try to make then be the connection between interactors and the view.
 - Use Dagger to maintain your object instantiation in a central place. This becomes even more important in larger projects.

### Dependency Injection

Both our MVP and our MVVM template projects use [Dagger 2](https://github.com/google/dagger) as the dependency injection tool.

## Stack

Check out our blog for a quick run through of our [internal stack](https://engineering.nodesagency.com/our-stacks/android/)

[**NStack SDK**](https://github.com/nodes-android/NStack) Offers dynamic localisation for our apps and other useful functionalities. If you go to [nstack.io](https://nstack.io/) and select your project and translate, you will be able to add new sections with new Translations. These translations can be changed in NStack and the changes will reflect in the app without the need of an app update.

[**Filepicker**](https://github.com/nodes-android/filepicker) As the title goes it is a library for picking a file and getting a URI back. Also deals with special and very complicated case of obtaining and image from the camera on Android.

[**Locksmith**](https://github.com/nodes-android/locksmith) Handling fingerprint/encryption can be verbose and complex, so we made a wrapper around handling it.

[**Form validation**](https://github.com/nodes-android/form-validator) Making validation in login/signup etc forms can be annoying and time consuming, so we tried making it easier and faster to develop.


## Commonly used thirdparty libs
### Retrofit/OkHttp
We use Retrofit for API communication (surprise)

### Glide
For image loading we usually use Glide over Picasso, since their defaults are usually less heavy in memory usage and the context lifecycle to bitmap cache freeing is also good.

### Firebase
In general we always use Firebase for analytics, crash reporting and apps distribution. Sometimes we add Remote Config or in rare cases their serverless database offerings.

### New Relic
This piece of software uses an brand of reflection to look at your apps HTTP communication, intercepting errors and uploading them to a cloud interface. This should be added when we're dealing with an externally developed API.

# Tools We Use
[**Slack**](https://nodes.slack.com) is our main communication tool.

[**Jira**](https://www.atlassian.com/software/jira) is our project management tool. [**Trello**](https://www.trello.com) is also used for some internal projects and older projects not yet migrated to Jira.

[**Harvest**](https://nodes.harvestapp.com/) is the time tracking tool that we use. Make sure to always harvest on the appropriate project. Ask the PM on which Harvest project you should track the time.

[**Forecast**](https://forecastapp.com/) is the planning tool that we use. Check Forecast when you get to work to have an overview of what you're assigned to for that day. If there are any changes the PM will let you know. If you finish earlier than expected and you are still allocated please tell your PM.

[**Postman**](https://www.getpostman.com/) is a great tool which helps you see and test web APIs. Our backend team uses Postman to test and document their APIs. Every project has its own library which can contain multiple folders with different endpoints.

[**Zeplin**](https://zeplin.io/) Is the tool we use to check the Android designs of each of our projects. In Zeplin, you can see each screen in the design, get info about the sizes, margins, paddings, fonts, colours and also export assets for our app. Try to use SVG's if possible and feel free to have multiples of 8 to follow the Android design guidelines.

[**Firebase Crashlytics**](https://firebase.google.com/docs/crashlytics) is the current crash reporting platform. We used to use Hockey for that.

[**Firebase App Distribution**](https://firebase.google.com/docs/app-distribution) is our primary tool for internal apps distribution and delivering latest test build to our clients.


[**NStack**](https://nstack.io/) is a service we built and it is the tool we use for doing localisation. Together with the [**NStack SDK**](https://github.com/nodes-android/NStack) it offers dynamic localisation for our apps and other usefull functionalities. If you go to [nstack.io](https://nstack.io/) and select your project and translate, you will be able to add new sections with new Translations. These translations can be changed in NStack and the changes will reflect in the app without the need of an app update.

# Coding Guidelines
Make sure to read our [**Android Style Guide**](https://github.com/nodes-android/guidelines/blob/master/styleguide.md).  


# CI
Our CI system is now hosted by Bitrise. Along with Firebase, Bitrise can be used to distribute our applications executables.
We have several tools used for building and deploying.
 - [Firebse App Distribution Step](https://github.com/guness/bitrise-step-firebase-app-distribution)
 - [Bitrise Deploy Step](https://github.com/bitrise-steplib/steps-deploy-to-bitrise-io)
 - [Changelog step (gathering commit messages for a changelog)](https://github.com/nodes-android/ci-bitrise-changelog-step)


# Working at Nodes
Having offices in many different timezones, from London to Dubai, means you might get to work with colleagues who are not in the same office as you are. If that is the case, make sure to rely as much as possible on Jira/Trello and the project's channel on Slack.

It helps a lot if you try to foresee any problems you might run into and let the PM know. This is to avoid the situation where you need an answer from someone in order to continue with your work. It is fine to take the lead and tackle design problems for example. Just make sure to let your PM know about the problem and the solution since the PM's must be always kept in the loop.

Estimate sprints before starting any ticket. If before starting a ticket you feel that the estimate has changed, re-estimate and let your PM know. Try to harvest per ticket copy-pasting the link of the ticket you are working on so the PM can have a clear overview of how the time is being used.

If you need something (an image asset, some input from the PM, etc), make a Trello card asking for it. If it is urgent you can ping them on Slack.

If there's some feedback from the client but you're not sure what to do about it ask your PM and explain the different options with an estimate of each one. Feel free to advise for the best solution but make sure you explain why.

If in doubt, have a talk to discuss things. Try to write down notes and conclusions of the meeting and share it with your project's channel if its a problem that affects other platforms.
