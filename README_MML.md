Amazon.com Confidential

#  Video Skill API integration with Fire TV App using ADM
<img src="https://m.media-amazon.com/images/G/01/mobile-apps/dex/alexa/alexa-skills-kit/tutorials/fact/header._TTH_.png" />

This README describes the steps necessary to integrate a Fire TV video streaming app with the *Alexa Video Skill API* using *Amazon Device Messaging (ADM)*. Following this integration end-users will be able to use their Echo or Fire TV voice remote to directly control the integrated Fire TV app.

Refer to the [Alexa Video Skill Client Library Introduction](Introduction.md) to get a high level overview of the architecture and also to learn about the VSK directives.

* Click [here](https://developer.amazon.com/docs/video/understand-the-video-skill-api.html) to learn about Alexa Video Skill API
* Click [here](https://developer.amazon.com/docs/fire-app-builder/overview.html) to get started with Fire App Builder
* Click [here](https://developer.amazon.com/docs/adm/overview.html) to learn more about Amazon Device Messaging

## Table of contents

* [Usage](#usage)
* [Clone the repository](#clone-the-repository)
	+ [Repository Contents](#repository-contents)
* [Quick Start Guide](#quick-start-guide)
	+ [Prerequisites](#pre-requisites)
	+ [Setup Lambda for the Skill](#setup-lambda-for-the-skill)
	+ [Register your Video Skill](#register-your-video-skill)
	+ [Complete Lambda Configuration](#complete-lambda-configuration)
	+ [Link Your Device to Video Skill](#link-your-device-to-video-skill)
	+ [Testing Your Video Skill](#testing-your-video-skill)
* [Full Integration](#full-integration)
	+ [Prerequisites](#pre-requisites-1)
	+ [Setup Existing Fire TV Application](#setup-existing-fire-tv-application)
		- [Add Alexa Video Skills Client Library to your Fire TV App](#add-alexa-video-skills-client-library-to-your-fire-tv-app)
		- [Configure Proguard](#configure-proguard)
		- [Initialize Video Client Library](#initialize-video-client-library)
		- [Initialize Down Channel service](#initialize-down-channel-service)
		- [Putting it all Together](#putting-it-all-together)
	+ [Setup Sample Fire TV App](#setup-sample-fire-tv-app)
	+ [Signing your Application](#signing-your-application)
	+ [Register your Fire TV app for Login with Amazon](#register-your-fire-tv-app-for-login-with-amazon)
	+ [Connect to Fire TV Through ADB](#connect-to-fire-tv-through-adb)
	+ [Create and Deploy Lambda Package (zip)](#create-and-deploy-lambda-package--zip-)
	+ [Testing Your Video Skill with Fire TV app](#testing-your-video-skill-with-fire-tv-app)
	+ [Customize Handling Alexa Directives](#customize-handling-alexa-directives)
* [Finalizing Your Skill Configuration](#finalizing-your-skill-configuration)
	+ [Prerequisites](#pre-requisites-2)
	+ [Skill Configuration Updates](#skill-configuration-updates)
	+ [Finalizing Your Lambda](#finalizing-your-lambda)
* [Testing Your New Auto-Pairing Skill for the First Time](#testing-your-new-auto-pairing-skill-for-the-first-time)
* [Additional Resources](#additional-resources)
	+ [Tutorials & Guides](#tutorials---guides)
	+ [Documentation](#documentation)

## Usage

```text
Alexa, play House of Cards
		>> Getting House of Cards from Fire TV Sample Skill

Alexa, change channel to CNN
		>> Tuning to CNN on Fire TV Sample Skill. This is The Lead with Jake Tapper
```
## Clone the repository

	bash
	$ git clone https://github.com/amzn/alexa-video-skills-kit-preview.git

### Repository Contents
* `/sample-nodejs-vsk-with-adm` - Sample [AWS Lambda](https://docs.npmjs.com/getting-started/installing-node) project with back-end logic for the Alexa Skill
* `/sample-alexa-firetv-app` Sample Fire TV app with Alexa integration using ADM
* `/resources`	- Stock images and othe resources to configure a sample skill


## Quick Start Guide

This section will cover a quick way to see the *video skill* in action with Alexa responding to user requests for video controls. You do not need to have the Fire TV app running for this portion, and you won't be sending messages using ADM at this time. After completion, you can follow the documentation to do a [full integration](#full-integration).

### Prerequisites

* Register for an [AWS Account](https://aws.amazon.com/)
* Register for an [Amazon Developer Account](https://developer.amazon.com/)

### Setup Lambda for the Skill

1. Sign in to https://aws.amazon.com/.

2. Ensure at the top right that you are in the "US East (N. Virginia)" region of AWS.

3. Navigate to https://console.aws.amazon.com/iam/home.

4. Select **Roles** from left navigation and click **Create role**.

5. Select **Lambda** under **AWS Service** and then click **Next: Permissions**.

6. Search for **AWSLambdaBasicExecutionRole**. Then, mark the checkbox next to **AWSLambdaBasicExecutionRole** and click **Next: Review**.

7. Give your role a name and description as you like, and click **Create role**.

8. Now, navigate once again to https://aws.amazon.com/.

9. Select **Service** in the top left menu, and click **Lambda** under **Compute**.

10. Click **Create function**.

11. Give your lambda a name, select Node.js 6.10 from the dropdown, and select your IAM role from the dropdown under **Existing role**.

12. Then, click **Create function**.

13. At the top right of the page, you should see a value called "ARN". Make a note of your Lambda's ARN, you will need it in next section.


<a name="register-your-video-skill"/>

### Register your Video Skill

1. In a new window/tab, sign in to https://developer.amazon.com/.

2. Navigate to https://developer.amazon.com/alexa-skills-kit and click **Start a Skill**.

3. Click the **Create Skill** button and then type in a simple name for your skill under the *SkillName* field. Click **Next** to continue.

	>The name should be easy and obvious for a user to say via voice. Additionally, your skill's name must not indicate that its functionality is limited. For example, you should not use "basic", "reduced" or "simple" in the name of your skill.

4. Select **Video** *Pre-Built Model* and click **Create Skill**.

5. Make a note of **Your Skill ID**. You will need to refer to this later.

6. Paste the Lambda's ARN into the **Default endpoint** and click **Save**.

7. Select **Account Linking** from the left navigation.

8. Paste *https://gogsot7zfi.execute-api.us-east-1.amazonaws.com/prod/dummyOauth* into the **Authorization URI**, **Access Token URI**, and **Client Secret** fields.

	>It’s perfectly OK to have dummy values in this skill configuration because end-users will never see your skill in this form, and Amazon will manually create a new skill for you once you are ready to move into production. In the meantime, please continue to use the dummy values provided in these instructions.

9. For Client ID, just put a value of **alexa-skill**.

10. Click **Add Scope** and give your scope a simple one-word name.

11. Select **HTTP Basic** next to **Client Authentication Scheme**.

12. Click **Save**.

13. Select **Launch** from top navigation, and ensure that all required fields for *Skill Preview*, *Privacy & Compliance*, and *Availability* in this section are filled in, including all icons (stock images are available in *resources* folder). You can modify these fields later if needed, but please at least put some stub values in each field for now.

14. Once fully configured you should see **Your skill is ready for submission**.
	>![Skill Fully Configured](./resources/images/configured-skill.png)


### Complete Lambda Configuration

1. Go back to the **Lambda Management Console** window/tab.

2. Under **Add triggers** select **Alexa Smart Home**.

3. Paste the Skill ID from Step 5 of previous section into the **Application ID** field. Ensure that *Enable trigger* is checked, and click **Add**.

	>If you're using "versioning" for your Lambda, then you will need to re-add the Smart Home Trigger each time that a new version is created.

20. Click **Save** in the top right of the page.

21. Now, in the top center of the page, click your lambda's name. This will take you to the code editor.

22. As part of this documentation, we’ve included a file called [Basic_Lambda_Code.js](./sample-nodejs-vsk-with-adm/Basic_Lambda_Code.js). Copy and paste all of the code from that file into the code editor VERBATIM. Do not modify that code or any lambda configurations at this time, and please do not omit any parts of the sample code when pasting.

23. Click **Save**.

### Link Your Device to Video Skill

1. Go to https://alexa.amazon.com and navigate to the **Music, Video & Books** section.

2. Ensure that *all* video skills are disabled. You can disable a video skill by clicking on the skill name, and then clicking “Disable” on the following screen.

3. In **Music, Video & Books** click on Fire TV and unlink any Fire TV/echo device pairings.

4. Now, return to the **Music, Video & Books** section, and select your new video skill from the menu.

5.  Click **Enable Skill** on the next screen.

6.	A pop-up window should open. Close this pop-up window, and wait for Device Discovery to complete (if necessary).

7.	Select **VSKTV** from the menu that comes up, and click **Continue**.
	>![Link to VSKTV](./resources/images/link-vsktv.png)

8.	Select the Echo device that you’d like to use for testing from the menu, and click **Link Devices**.
	>![Device Linked](./resources/images/device-linked.png)


### Testing Your Video Skill


1. Now, navigate to https://console.aws.amazon.com/cloudwatch/ and click on **Logs**.

2. Click on the lambda that you created for your video skill.

3. Now, say to your Echo *Alexa, watch House of Cards*. Your Echo should respond *Getting House of Cards from 'Your Skill Name'*.

4. You should now be able to see the **Play** directive for **House of Cards** in your lambda logs. If you expand the log message, it will look like the below:
	>![Cloud Watch Log](./resources/images/cloud-watch-log.png)

5. You should now try several other utterances. Your lambda will receive those utterances, and your lambda code will be able to act upon them.

6. At this point, you are able to begin experimenting with directives and commence with further integration of your Fire TV app.

**Congratulations! You are now finished setting up your simple Video Skill and Lambda.**





<a name="full-integration"></a>
## Full Integration

The remainder of this document will cover how to take the basic skill you created and have it interact with a Fire TV app using Amazon Device Messaging (ADM). You should have some familiarity with [Node.js and NPM](https://www.npmjs.com/) and with [Fire App Builder](https://developer.amazon.com/docs/fire-app-builder/customize-the-application-project.html) to finish this section. Also, read **[Interpreting and Reacting to Directives](directives.md)** to understand video skill directives from Alexa.

<a name="pre-requisites-1"/>

### Prerequisites

* Install [Node.js (> v6.1) and NPM](https://docs.npmjs.com/getting-started/installing-node).
* Install Java JDK (>= v1.8).
* Install [Android Studio](https://developer.android.com/studio/index.html).

### Setup Existing Fire TV Application

If you already have a Fire TV application, follow steps listed in this section. For using the bundled [Sample Fire TV App](./sample-alexa-firetv-app), skip this section and go straight to [Setup Sample Fire TV App](#bundled-application).

#### Add Alexa Video Skills Client Library to your Fire TV App

For Fire TV apps hoping to be Alexa-enabled, Amazon requires that partners embed the Alexa Video Client Library into their app. The inclusion of the Alexa Video Skills Client Library in your Fire TV app will allow you to:

1. Authenticate with Amazon using Login with Amazon(LWA). If you are new to LWA, read the [Login with Amazon for Android](https://developer.amazon.com/public/apis/engage/login-with-amazon/content/android_docs).

2. Automatically pair your skill with an Echo device by inferring a relationship from an existing Echo to Fire TV pairing (rather than requiring a customer to log in through the Alexa App).

3. Manage application lifecycle events and send those events back to Alexa, which will help Alexa make more intelligent decisions when routing users to Video Skill API-integrated apps.

4. Send Alexa responses to directives received in your application.

If you are using Gradle with Android Studio, you can integrate Alexa Video Skills Client Library using the AAR file contained in the released SDK.
To add the Alexa Video Skills Client Library(AlexaClientLib.aar) into your project in Android Studio, do the following:

1.	Go to **File > New > New Module**.

2.	Select "Import .JAR/.AAR Package" and click next.

3.	Enter the path to AlexaClientLib.aar file located under **/resources**  and click finished.

4.	Go to **File > Project Structure**.

5.	Under **Modules** in the left menu, select **app**.

6.	Go to **Dependencies** tab.

7.	Click the **+** button in the bottom and select **3. Module Dependency**.

8.	Select the **AlexaClientLib** from the list.

#### Configure Proguard

If you’re using Proguard, edit the proguard.cfg file and add the following configuration:
```
-libraryjars ../AlexaClientLib

# Keep the LWA and VSK Client Library classes
-dontwarn com.amazon.identity.auth.device.**
-dontwarn com.amazon.alexa.vsk.clientlib.**

-keep class com.amazon.identity.auth.device.** { *; }
-keep class com.amazon.alexa.vsk.clientlib.** { *; }
```

#### Initialize Video Client Library

Alexa Video Skills Client Library must be initialized for the client library to work correctly. To do so, you must:
* Declare initializeAlexaClient() in your Application class (*see code sample below*).
* Replace *YOUR_SKILL_ID* with the Skill Id from [Register your Video Skill](#register-your-video-skill) section.
* Call *initializeAlexaClient()* from *onCreate()*.

```
public class YourApplication extends Application {
		@Override
    protected void onCreate() {
        super.onCreate();
        …
        // Initialize the Alexa Video Skills Client Library first.
        initializeAlexaClientLibrary();
     }


    public static void initializeAlexaClientLibrary(Activity mainActivity) {
        // Retrieve the shared instance of the AlexaClientManager
        AlexaClientManager clientManager = AlexaClientManager.getSharedInstance();

        // Gather your Skill ID and list of capabilities
        final String alexaSkillId = "YOUR_SKILL_ID";

        // Create a list of supported capabilities in your skill.
        List<String> capabilities = new ArrayList<>();
        capabilities.add(AlexaClientManager.CAPABILITY_CHANNEL_CONTROLLER);
        capabilities.add(AlexaClientManager.CAPABILITY_REMOTE_VIDEO_PLAYER);
        capabilities.add(AlexaClientManager.CAPABILITY_PLAY_BACK_CONTROLLER);
        capabilities.add(AlexaClientManager.CAPABILITY_SEEK_CONTROLLER);

        clientManager.initialize(mainActivity,
            alexaSkillId,
            AlexaClientManager.SKILL_STAGE_DEVELOPMENT,
            capabilities);
    }
}
```

Note: If you'd like to declare a capability without a predefined constant in the client library, you may do so by using the exact string name of that capability. For example, you would add the "Launcher" capability for GUI shortcuts as follows:

```
	capabilities.add("Alexa.Launcher");
```

#### Initialize Down Channel service

To receive directives from the Alexa Service, you have to construct a down-channel service.
* When your down-channel service is ready, call *setDownChannelReady()* API.
	- The first parameter (isDownChannelReady) should be “true” to indicate that your down-channel is ready.
	- The second parameter (appInstanceId) should be a string that uniquely identifies your application instance. If you are using ADM, you should the app’s ADM Registration ID for this parameter. However, it’s perfectly acceptable to use a technology other than ADM if you prefer not to use ADM.
* When your down-channel service status is changed, you have to call setDownChannelReady() API each time with proper status flag value.
* Initialize ADM service at application creation time as shown below.

```
public class YourApplication extends Application {

		@Override
    protected void onCreate() {
      super.onCreate();

      // Initialize the Alexa Video Skills Client Library first.
      initializeAlexaClientLibrary();

      // Initialize ADM.
      initializeAdm();
    }

    private void initializeAdm() {
      try {
       final ADM adm = new ADM(this);
          if (adm.isSupported()) {
            if (adm.getRegistrationId() == null) {
              // ADM is not ready now. You have to start ADM registration by calling
              // startRegister() API. ADM will calls onRegister() API on your ADM
              // handler service when ADM registration was completed with registered ADM id.
              adm.startRegister();
            } else {
              // [IMPORTANT]
              // ADM down-channel is already available. This is a common case that your
              // application restarted. ADM manager on your Fire TV cache the previous
              // ADM registration info and provide it immediately when your application
              // is identified as restarted.
              //
              // You have to provide the retrieved ADM registration Id to the VSK Client library.
              final String admRegistrationId = adm.getRegistrationId();
              Log.i(TAG, "ADM registration Id:" + admRegistrationId);

              // Provide the acquired ADM registration ID.
              final AlexaClientManager alexaClientManager = AlexaClientManager.getSharedInstance();
              alexaClientManager.setDownChannelReady(true, admRegistrationId);
           }
         }
       } catch (Exception ex) {
          Log.e(TAG, "ADM initialization is failed with exception", ex);
      }
   }
}
```

>Do not pass false during application initialization unless you are certain the current user does not have access to content. Toggling this state off and on will result in Alexa unregistering and re-registering the endpoint which will have unintended consequences to your skill.

#### Enable/Disable Alexa Targeting for the App when user signs in/out

The function setAlexaEnabled() is used to enable or disable an app instance as a targetable endpoint by the user. Within your app, you should called setAlexaEnabled(true) when the user logs into your app, and you should call setAlexaEnabled(false) when the user logs out. The only exception to this rule is if your app does not have a concept of users logging in or out, and your content is available to users regardless of if they’re logged in or have a particular subscription.
```
// User is logged in with active subscription
AlexaClientManager.getSharedInstance().setAlexaEnabled(true);

// User is logged out or does not have a valid subscription
AlexaClientManager.getSharedInstance().setAlexaEnabled(false);
```

#### Putting it all Together

Follow the instructions from the links below to complete the integration.

* [Update your app manifest](https://developer.amazon.com/docs/adm/integrate-your-app.html#update-your-app-manifest).
* [Implement handling for registration and messages](https://developer.amazon.com/docs/adm/integrate-your-app.html#implement-handling-for-registration-and-messages).
* [Gracefully degrade if ADM is unavailable](https://developer.amazon.com/docs/adm/integrate-your-app.html#gracefully-degrade-if-adm-is-unavailable).

<a name="bundled-application"/>

### Setup Sample Fire TV App


Follow [these instructions](https://developer.amazon.com/docs/fire-app-builder/download-and-build.html) to build and deploy the sample Fire TV application provided with the document. If you are configuring an existing application, skip this section and go directly to [signing your application](#sign-app).
>Make sure you use the included *sample-alexa-firetv-app* and not fork the GitHub project referenced in the [link](https://developer.amazon.com/docs/fire-app-builder/download-and-build.html). The included application has been updated to use **[Video Skill API](https://developer.amazon.com/docs/video/understand-the-video-skill-api.html)** in place of Amazon Media Session.


* Open **TenFootApp.java** under *TVUIComponent/lib/src/main/java/com/amazon/android/tv/tenfoot/base/* and set   ALEXA_SKILL_ID to Skill Id from [Register your Video Skill](#register-your-video-skill).

* Go to **Build > Clean Project** to clean up any artifacts from the previous app. (This process takes several minutes.)

* Build your new app by clicking the **Build > Rebuild Project** button.

Review **TenFootADMessageHandler.java** under *TVUIComponent/lib/src/main/java/com/amazon/android/tv/tenfoot/receiver/* to see how ADM messages from Alexa are being handled.

<a name="sign-app"/>

### Signing your Application

You are required to sign your application before it can be installed. Signing your application will generate the MD5 and SHA-256 signatures needed for ADM configuration. Follow [these instructions](https://developer.android.com/studio/publish/app-signing.html) on how to sign the Fire TV app. Make sure to:

1. Sign in to https://developer.amazon.com/myapps.html.
2. Select your Fire TV application entry.
>![My Apps](./resources/images/MyApp.png)

3. Click **Device Messaging**.

4. Choose an existing security profile from Select a Security Profile or click Create a New Security Profile. A security profile provides the OAuth credentials that you use when sending messages with ADM.

5. If you used an existing security profile, be sure to select Confirm to save your changes.
6. Click View Security Profile. From this screen copy and save Client ID and Client Secret.
>![Security Profile](./resources/images/SampleAppSecurityProfile.png)

7. Click **Kindle/Android Setting**

8. Create an API key. Your app requires one or more API keys.

	(Required) For a pre-release or "debug" version of your app. In all cases, you must create an API key for the debug version of your app, in order to test your app with ADM.

	(Optional) For a release or "production" version of your app. If you sign the release version of your app using your own certificate, you must create an additional API key for the release version of your app. If you allow Amazon to sign your app on your behalf, you do not need to create an additional API key.

	To create an API key, you must provide both the package name (for example, com.mycompany.bestapplication) for the app and its signature:

	Debug application signature for the pre-release version of your app.

 	>**You must provide both an MD5 and an SHA-256 signature that was generated during [Signing Your Application]().**

	Locate the keystore file that is used to sign your app, as well as the alias of the certificate within the keystore file used for signing. For example, if the keystore file is named my_key_store and the certificate within the keystore that is used for signing is called my_certificate_alias, you would run the following command:

	*{JAVA_HOME}/bin/keytool -list -v -alias my_certificate_alias -keystore my_keystore_file*

	The output should contain an MD5 or SHA-256 value similar to the following. This is your application signature.

	```
	MD5: 2E:0B:46:F8:D0:4A:06:AC:18:7A:2E:B0:42:95:58:FE

	SHA-256: 34:A3:65:30:3E:B2:9B:30:DD:10:E8:87:79:16:DF:4C:61:77:BC:1F:80:BD:3F:12:39:A3:31:E6:68:19:1C:6F
	```

9. Click Generate New Key. You can get key details by clicking on **Show** link within "Android/Kindle Settings".
>![API Key Details](./resources/images/GenerateAPIKey.png)


### Register your Fire TV app for Login with Amazon

For a pre-release or "debug" version of your app, you must copy your API key from the previous section and store it as the only data in a file named api_key.txt. The api_key.txt file must be located inside the assets folder for your project.

> Important: ADM cannot recognize your API key if it contains any spaces or line breaks.

For a release or "production" version of your app, you might not need to create an API key. Only if you sign the release version of your app using your own certificate, must you create an additional API key for the release version of your app. Otherwise, if you allow Amazon to sign your app on your behalf, you do not need to create an additional API key.

### Connect to Fire TV Through ADB

Follow [Connect through ADB  instructions](https://developer.amazon.com/docs/fire-app-builder/connecting-adb-to-fire-tv.html) to launch your application in Fire TV.

If you are using the bundled sample app, look for **ADM registration Id** in the console logs to get the Registeration Id. If you are using an exsiting app, retrieve the *ADM Registration Id* from *initializeADM* method. Save this value for later.


### Create and Deploy Lambda Package (zip)

Now that we have the Fire TV app running, we need to update the Lambda to be able to pass the directives along.

1. Open a **index.js** under *sample-nodejs-vsk-with-adm* using a text editor.

2. Enter the values of **CLIENT_ID** and **CLIENT_SECRET** from  step 6 of [Signing your Application](#signing-your-application).

4. Enter the value of **admRegistrationId** in function *sendMessageToDevice* from [Connect to Fire TV Through ADB](#connect-to-fire-tv-through-adb).

5. Save the file.

6. Now, open a terminal window.

	On a Mac, you can open Terminal by pressing Cmd + spacebar and then typing Terminal. On Windows, you open the Command Prompt usually by typing cmd in your program search. (The exact steps vary based on your Windows version.)

7.	Run command `cd <path to sample-nodejs-vsk-with-adm>` to change current directory location.
```
$ cd /Users/<your username>/skills/sample-nodejs-vsk-with-adm/
```
8.	Run command `npm install`. This should create a folder called node_modules (if not already present) and add all the dependent packages in it.
```
$ npm install
```
9.	Run command `zip -r sample-nodejs-vsk-with-adm.zip .` to create a zip file with all the lambda code packaged together. On Windows OS you can also right click on the folder in Windows Explorer and choose *Send To -> Compressed (Zipped) Folder*.
```
$ zip -r sample-nodejs-vsk-with-adm.zip .
```

10. Once again sign in to https://aws.amazon.com/.

11. Select **Service** in the top left menu, and click **Lambda** under **Compute**.

12. Click on the Function Name that was created in [Setup Lambda for the Skill](#setup-lambda-for-the-skill).

13. Under **Function Code** change *Code entry type* to **Upload a .ZIP file**.

14. Click **Upload** and select the zip file created in step 4 above.

15. Click **Save**.

### Testing Your Video Skill with Fire TV app

#### If you are using the bundled Sample Fire TV app

1. Say to your Echo *Alexa, play San Lucas Trip video*.

2. Your Fire TV app should play a video titled *San Lucas Trip*.

3. Say to your Echo *Alexa, pause* and this should pause the video. To resume say *Alexa, play*.

4. Say *Alexa, find diving videos* to see search results for diving videos.

#### If you are using an exsisting Fire TV app

1. Say to your Echo *Alexa, play 'video name'* or *Alexa, find 'video name'*.

2. Check your ADM message handler in the app, to see if you have reveived the directive.


### Customize Handling Alexa Directives

Add logic to handle the directives from Alexa to **index.js** file under *sample-nodejs-vsk-with-adm* and repeat steps 3 and 4 from the previous section


## Finalizing Your Skill Configuration

**Auto-Pairing**

So far, you have been using a skill which you had manually enabled from the Alexa app. Now that you’ve incorporated the client library, we can transform your skill from a **manually-paired** skill into an **auto-paired** skill. Auto-paired skills do not require users to manually enable the skill through the Alexa app. They get enabled for the user automatically through the client library when the user launches the app. This is possible because Amazon can infer the device relationships from the already-existing Fire TV skill. All Fire TV Video Skill API integrations are required to be auto-paired skills in order to be certified. After your skill is transformed into an auto-paired skill, the client library can begin to do its magic. 

**Internationalization**

Starting with version 1.4.0, the client library supports international regions. The library takes into account the marketplace within which the user's Amazon account is registered, and then performs pairing with Alexa in that region for the user. For this flow to work end-to-end, you must ensure that you have provided region-specific catalogs to your Amazon solution architect, or at least indicated that you would like your US catalog to also be available in specific regions. Once your skill is updated with these details, your users in international regions will be able to auto-pair and use voice with Alexa to control your app.

**Skill Discovery**

You should also be aware that after your skill is transformed, your skill will disappear from the Alexa skill store in the Alexa app, and will instead become part of the Fire TV video skill (from a user’s perspective). Your Amazon Solution Architect contact can provide more details on that, but the summary is: Alexa-enabling your app will be seamless from the eyes of the user, and they will not have to look for your skill in the skill store to use voice with your skill. We’ve found that this greatly increases adoption of voice functionality among users. 

**Alexa Directives**

Additionally, after your skill is transformed, the directives you receive will be slightly different. Right now, you receive directives even if the user asks to play content that is not in your catalog. After your skill is transformed, this will no longer be the case; instead, you will receive only directives that are relevant to your app based on your catalog. A direct consequence of this is that you won’t have to iterate through multiple entities to find the entity with your catalog ID; it will simply be the first entity in the list you receive (please test this in your lambda to confirm). 

**Live App Testing**

One final thing to be aware of is that, after this configuration update is complete, you will no longer be able to test the Alexa capabilities of your app by side-loading dev builds onto a Fire TV. Instead, you will need to submit a **Live App Test** to the Fire TV Appstore to test the Alexa functionality, including the auto-pairing capabilities of the client library. Please ensure that you have completed as much of your skill and app development as possible prior to making the below configuration changes because this change is irreversible, and it will have a meaningful impact on your ability to develop, test, and iterate on your Video Skill API integration rapidly. You must reach out to your Amazon Solution Architect to make the above changes; they must be made manually in the Amazon back-end.

<a name="pre-requisites-2"/>

### Prerequisites

1.	To avoid any issues when these updates happen, first disable your video skill in your Alexa App.

2.	Ensure that any other testers have also disabled the video skill in the Alexa app.

3.	Then, go to https://alexa.amazon.com, log in, click Smart Home, click Devices, and click “Forget All”.

4.	Now, go to the Music, Video & Books section of the Alexa app.

5.	Click Fire TV.

6.	Ensure that the Fire TV video skill is enabled, and ensure that the Fire TV is paired with the Echo you’d like to use for testing.

7.	All testers of your Video Skill API integration must complete these steps at this time.

8.	Provide the LWA Client ID of the Security Profile associated with your Fire TV App to your Amazon Solution Architect, and let them know that this LWA Client ID must be whitelisted for the “write” and “proactive_enablement” scopes (required for the client library to work).

### Skill Configuration Updates

1.	Please ensure that all steps in the “Prerequisites” section are complete before proceeding.

2.	Submit a new Live App Test to the Fire TV Appstore and please make sure to add your Amazon Solution Architect to that Live App Test.

3.	Please ensure that steps 4-7 below are consolidated into a single email to your Amazon Solution Architect (for simplicity).

4.	Let your Amazon Solution Architect know that you’ve submitted this Live App Test and added them to it. Ask them to update your skill configuration to associate the submitted app’s LWA Client ID with your skill configuration.
>Please note that Amazon will read the submitted Live App Test app’s logs to determine the LWA Client ID to use. This LWA Client ID may be different from the LWA Client ID you used in Step 8 above. This is because Amazon re-signs your app’s API key using a different LWA Client ID once you submit a Live App Test or to Prod, and this LWA Client ID is not visible to you, the developer. Please ensure that you do not insist on using a particular LWA Client ID during this step, and instead let your SA contact know to simply use the submitted app to infer the correct LWA Client ID to use.

5.	When you do this, you must also provide your video skill’s Skill ID to your SA contact. This is the same Skill ID you obtained in Step 5 of the “Registering Your Video Skill” section.

6.	Please also let your Amazon Solution Architect know that you need to have your skill’s PAMS Partner ID updated. All auto-paired skills require this update, and if it’s not made, you will have trouble editing your skill configuration in the future.

7.	Finally, let your SA contact know which catalog you’ve been using from the “externalIds” field, so they can ensure that your skill is associated with the correct catalog.

After your SA contact has completed the necessary configuration update, your skill will be enabled for auto-pairing. Additionally, your skill will now also receive directives directly from the Fire TV voice remote’s microphone.

### Finalizing Your Lambda

So far, you have been simply hard-coding an ADM Registration ID for your testing. As you scale to more users, this needs to change. Instead of simply communicating between your lambda and one user, you need to be able to communicate from your lambda to all your users. You will uniquely identify those users through the Application Instance ID passed into their app’s client library initialization. If you’re using ADM, that Application Instance ID should be the ADM Registration ID. To find the Application Instance ID in the lambda, you should look at the new “cookie” section within the directives, and within that section, find a field named “applicationInstanceId”. This is the field that contains your App Instance ID / ADM Registration ID. You should use this unique identifier to determine where to send the directive from the lambda to your app, rather than hardcoding to just one test device.
```
{
    "directive": {
        "header": {
            "namespace": "Alexa.PlaybackController",
            "name": "FastForward",
            "payloadVersion": "3",
            "messageId": "c934573d-a72a-474e-86d7-d4284759c829",
            "correlationToken": "AAAAAAAAAABz/cBzCAf/WqtdDQgXg3ljcAEAAAAAAADStuqEh0EHcdBvtW+vl2QdQc5ZS/NRHrdxX2ITRtFvOegAtWpVEmalGK4gu/Yi7nu/ahsRaXtzL2iZyK/QnL14GYA1bQsELoUJg2f99dATxQNWkXFhalXEEN7viyTkx2ITgKF+opoBmBGuLzqhvIb2KzEdo2CMC0Oa6k2F3Iza60G37AO8HfVcN6I5G6W/u40484xv7KmAgDavDeZmnS1/zCbdS7J8koyvSvlCHVVp0TaYdLDJ5E0EvIxKA2cDWfr8EKdcEJryUD0PhMyvBkKdCqAUyelrrCetO4sI9TmOPdQcuqt+WAdjRYKhwMOphkMAtV0wr0Mdh7xSR/KD+A6NRWg0p5j7rgTGVs0/ndgj4+lgHDLmGYkr9wvwm2FdT+mTvTf+peTBi39eiVEN0fhJ7bhIOn4tHQfL9Vgwh0HCGN/Q9ibHcqiszoTXEendfLrUlTSYgpDRwfI8UtJkL1cXHk372UPdqUGQ2p5llE/dpA=="
        },
        "endpoint": {
            "scope": {
                "type": "BearerToken",
                "token": "2d0a58d5-75ea-befc-18b8-239bb567260d"
            },
            "endpointId": "Please ignore this field.",
            "cookie": {
                "deviceType": "sloane",
                "applicationInstanceId": "This is the App Instance ID / ADM Registration ID",
                "appName": "Test",
                "appPackageName": "com.test.plus",
                "deviceId": "G0123V0955123456"
            }
        },
        "payload": {}
    }
}
```
Additionally, as mentioned in the previous section, the directives you receive in your lambda will now contain your catalog ID in the first entity, which could potentially simplify the logic you use to process directives. If you determine that code updates are needed for that, please make them now.

Lastly, now that the skill has undergone the conversion to a fully-fledged VSK skill, you can remove a portion of the endpoints code from the Discover.Response payload (if you haven’t already). The following section is no longer needed and its removal will help ensure a smooth transition for previous testers.
```
"endpointId": "VSKTV",
      "description": "VSKTV is the default device used for video skills developed for Fire TV integrations",
      "friendlyName": "VSKTV",
      "manufacturerName": "Amazon"
```

**Congratulations;** you have now completed the Alexa integration for your Fire TV app! Before you submit your skill for certification, ensure that you’ve completed the steps in [Certification Checklist.docx](resources/Certification_Checklist.docx). Thank you for your hard work!

## Testing Your New Auto-Pairing Skill for the First Time

1.	Delete all previously installed apps from your Fire TV.

2.	Update your Fire TV to the latest version of the operating system.

3.	Submit a new Live App Test with your new client-library-enabled app, and send it to your Fire TV.

4.	Once the app downloads, launch the app, and log into your account.

5.	Wait 5 seconds (just in case).

6.	Go back to the Fire TV home page.

7.	Using the Fire TV remote, say **Launch &lt;app name&gt;**. Your app should launch, and Alexa should say “This app is now Alexa enabled”.

8.	Then, using your Fire TV remote, say **Watch &lt;title&gt;**, where “title” is something in your CDF catalog. Playback should start.

9.	Other phrases to try:
 >a. Channel Change: “Tune to &lt;channel&gt;”

 >b. Transport Controls: “Pause”

 >c. Search: “Find &lt;title&gt; on &lt;app name&gt;”

**To test the above phrases using your Echo instead of your Fire TV remote, do the following:**

1. Go to http://alexa.amazon.com.

2. Click on Smart Home -> Devices -> Forget All.

3. Go to Music, Video, and Books, and click Fire TV. Ensure that your Echo is paired with your Fire TV here.

4. Go to http://developer.amazon.com, and navigate to your video skill configuration.

5. If a beta test is currently running for your video skill, end the beta test.

6. Begin a new beta test, and add new testers.

7. Ensure that all beta testers accept the new beta test invite that comes via email.

8. Try using the phrases from steps 7-9, prefixed with “Alexa” to control your Fire TV app through the Echo.


## Additional Resources

### Tutorials & Guides
* [Control Content with Video Skill API](https://developer.amazon.com/blogs/alexa/post/1314314b-9b98-4501-9032-f72c000a59b5/how-to-control-your-content-with-the-video-skill-api)
* [Create a Login with Amazon Project](https://developer.amazon.com/docs/login-with-amazon/create-android-project.html)

### Documentation
* [Official Alexa Skills Kit Node.js SDK](https://www.npmjs.com/package/alexa-sdk) - The Official Node.js SDK Documentation
*  [Official Alexa Skills Kit Documentation](https://developer.amazon.com/docs/ask-overviews/build-skills-with-the-alexa-skills-kit.html) - Official Alexa Skills Kit Documentation

Amazon.com Confidential