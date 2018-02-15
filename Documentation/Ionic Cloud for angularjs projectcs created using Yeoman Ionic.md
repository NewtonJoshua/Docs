![](\"https://upload.wikimedia.org/wikipedia/commons/thumb/d/d1/Ionic_Logo.svg/2000px-Ionic_Logo.svg.png\")  

## **Ionic Platform**:

## Build, push, deploy, and scale your Ionic apps, the easy way.

* * *

**Title Description:**  

The Ionic Platform is a cloud platform for managing and scaling cross-platform mobile apps. Integrated services enable you and your team to build, deploy, and grow your apps efficiently.

**Document Objective:**  

Ionic Platform works well with the standard Ionic projects. But projects following any Non-standard Directory Structure may face a few hurdles. This documents provides the steps to use Ionic Platform in the Ionic projects created using Yeoman.

**Document Scope:**  

This document covers the basic steps for creating an Ionic project using Yeoman and integrating it with Ionic Platform using the Ionic Platform Web Client. This document covers the basic steps to utilize Ionic Deploy, Ionic Analytics and Ionic Push.

**Intended Audience:**  

The intended audience for this document is Web/Mobile App developers, with both beginner and expert level expertise, who are familiar with the below Prerequisites.

**Prerequisites:**  

You should be familiar with the following frameworks/tools before trying this document.

*   AngularJs: [https://docs.angularjs.org/guide](\"https://docs.angularjs.org/guide\")

*   IonicFramework: [http://ionicframework.com/docs/guide](\"http://ionicframework.com/docs/guide\")

*   Yeoman: [http://yeoman.io/codelab/index.html](\"http://yeoman.io/codelab/index.html\")

*   Ionic Generator: [https://github.com/diegonetto/generator-ionic](\"https://github.com/diegonetto/generator-ionic\")

*   Ionic Platform: [https://ionic.io/platform](\"https://ionic.io/platform\")

* * *

## **Ionic Framework generator**

![](\"https://camo.githubusercontent.com/2aa3496f7bf9aeb758477ce01961748a486af0fe/687474703a2f2f692e696d6775722e636f6d2f4247727432514b2e706e67\")  

## A generator for the Ionic Framework from Yeoman, the Web's Scaffolding tool for modern webapps

Node.js is a JavaScript runtime built on Chrome's V8 JavaScript engine. npm is the package manager for JavaScript. Download and install Node (and npm) from [http://nodejs.org](\"http://nodejs.org\")

    $ npm install npm –g
    $ npm install -g yo

Yeoman helps you to kick-start new projects, prescribing best practices and tools to help you stay productive.

    $ yo ionic [app-name]

In _package.json_ include the following in devDependencies

    "grunt-string-replace": "^1.2.1"

In _bower.json_ include the following in dependencies

    "ionic-platform-web-client": "^0.7.1"

In _Gruntfile.js_ change the _scripts_ folder to _‘js’_. Change in _index.html_ too if required.

    grunt.initConfig({   yeoman: {…………    scripts: 'js',    ………… } })

Then run

    $ bower install && npm install
$ grunt$ grunt serve$ cordova platform add android $ grunt build:android --debug

* * *

## **ionic-platform-web-client**

![](\"http://ionicframework.com/img/ionic-logo-blog.png\")  

## A web client that provides interactions with the Ionic platform.

We need some code to let your app talk to the Ionic Platform. We need to add the Ionic platform web client for the Ionic app to interface with the plugins and the Ionic.io platform.

    $ ionic io init

In your _app.js_ add the _‘ionic.service.core’_ module dependency. In _Gruntfile.js_ add the grunt task _‘ionicSettings’_ as given below.


   

     grunt.initConfig({  
       ionicSettings:JSON.stringify(grunt.file.readJSON('./.io-config.json')),
       ionicIoBundlePath:'www/bower_components/ionic-platform-web-client/dist/ionic.io.bundle.min.js',
       'string-replace':{  
          ionicSettings:{  
             files:{  
                '<%= ionicIoBundlePath %>':'<%= ionicIoBundlePath %>',
    
             },
             options:{  
                replacements:[  
                   {  
                      pattern:'"IONIC_SETTINGS_STRING_START";"IONIC_SETTINGS_STRING_END"',
                      replacement:'"IONIC_SETTINGS_STRING_START";var settings =<%= ionicSettings %>; return { get: function(setting) { if (settings[setting]) { return settings[setting]; } return null; } };"IONIC_SETTINGS_STRING_END";'
                   }
                ]
             }
          }
       },
       copy:{  
          ionicPlatform:{  
             expand:true,
             cwd:'app/bower_components/ionic-platform-web-client/dist/',
             src:[  
                '**'
             ],
             dest:'www/bower_components/ionic-platform-web-client/dist'
          }
       }
    });grunt.registerTask('ionicSettings',
    [  
       'copy:ionicPlatform',
       'string-replace:ionicSettings'
    ]);

Add the _'ionicSettings'_ in _init_ and _compress_ tasks after _copy_.  
In _index.html_ move the below tag after all the tag declarations.

    <script src="bower_components/ionic-platform-web-client/dist/ionic.io.bundle.min.js"></script>

Then run

    $ Grunt serve

* * *

## **Ionic Deploy**

![](\"https://ionic-apps.s3.amazonaws.com/img/dashboard/deploy-header.png\")  

## Push real-time updates to your production apps, and manage version history.

Ionic Deploy lets you update your app on demand, for any changes that do not require binary modifications, saving you days, or even weeks, of wait time. Follow the below procedure to configure Ionic Deploy for your App.

In _Gruntfile.js_ add the grunt task _‘deploy’_ as given below.

  

    grunt.registerTask('deploy',
    function (){  
       return grunt.task.run(   [  
          'init',
          'ionic:upload'      + this.args.join()
       ]   );
    });

     

then run

    $ ionic plugin add ionic-plugin-deploy

Ionic Deploy Code:

   

    var deploy = new Ionic.Deploy();// Check Ionic Deploy for new codedeploy.check().then(function(hasUpdate){  
    
    },
    function(err){  
    
    });// Update app code with new release from Ionic Deploydeploy.update().then(function(result){  
    
    },
    function(error){  
    
    },
    function(progress){  
    
    });

Deploying Updates:

Send out new code for your app.

Create an apk and install your app. Make few changes in your code and deploy the changes using '_grunt deploy_'. Then update it from your app.

You can also deploy it from the _apps.ionic.io_ dashboard. You can deploy the app without the deploy parameter. Then, in the dash board you can add the metadata and versioning details and deploy the app from there.

        $ grunt build:android --debug
    $ grunt deploy --note "release notes"
    $ grunt deploy --note "release notes" --deploy=production

* * *

## **Ionic Analytics**

![](\"https://ionic-apps.s3.amazonaws.com/img/analytics/icon-events.png\")  

## View the live feed of events or the raw / unique number of events / users over time.

How many users are on your app right now? How many of those will use your app tomorrow, or next week? Without information, you have no way of telling if your app is being used in the ways that you expect. Follow the below procedure to configure Ionic Analytics for your App.

In your _app.js_ add the ‘_ionic.service.analytics_’ module dependency after the _ionic.service.core_  
Run the analytics register method in our module's run function.

    $ionicAnalytics.register();

In Ionic Analytics, each tracked action a user makes in your app is represented by an event object. An event is a single action done at a specific point in time. To track your own events, call `$ionicAnalytics.track(eventType, eventData)` whenever an action occurs.

    $ionicAnalytics.track('User Login', {  user: $scope.user});

The _ion-track-tap_ directive sends an event when its host element is tapped. The associated _ion-track-data_ directive attaches event data.

    <button ion-track-tap="eventType" ion-track-data="expression"></button>

In the _apps.ionic.io_ dashboard you can view the following analytics data,

Events: View the raw number of events over time, or the number of unique users who completed an event. An event can be anything from a user loading the app, to confirming a purchase.

Funnels: A funnel is a sequence of actions that you expect users to take in your app, leading up to a defined goal. Thoughtful use of funnels will let help you improve conversion rates.

Segments: View events over time, grouped by a specified property. Or, calculate the percentage of events that match a given property. Segments helps you understand your user base and see how properties change over time.

Retention: Track how long users are active on your app before they stop using it. Or, identify how long it takes for users to reach a defined goal, like a completed sale.

Pulse: A live feed of events coming in from your users.

* * *

## **Ionic Push**

![](\"https://ionic-apps.s3.amazonaws.com/img/dashboard/push-header.png\")  

## Send targeted and automated push notifications to your users.

Ionic Push lets you create targeted push notifications through a simple dashboard that will be sent automatically when users match specific criteria, and offers a simple API to send push notifications from your own servers.

**Android Push Profiles:**

Android push notifications use the _Google Cloud Messaging_ (GCM) service. Open the _[Google Developers Console](\"https://console.cloud.google.com\")_ and create a project. Copy down your _project number_. This will be the _GCM sender ID_ or **_GCM Project Number_**.

In the _API Manager_ section, enable the _Google Cloud Messaging API_. Then navigate to _Credentials_ section and select Create credentials, then choose API Key, then Server Key. Name your API key and leave the _Accept requests from_... field blank and click _Create_. Save your **_API key_**!

**Authentication:**

Go to your app's dashboard on the [Ionic Platform](\"https://apps.ionic.io/apps\") and navigate to _Settings -> Certificates_. If you haven't already, create a new security profile, then hit _edit_. Note down the **_Profile Tag_**.

Now, click the _Android_ tab and find the section marked _Google Cloud Messaging_, enter the _API Key_ you generated on the Google Developer Console, then click _Save_. Go to _Settings -> API Keys_. Under _API Tokens_, create a new token and copy it. This will your **_API Token_**.

    $ ionic plugin add phonegap-plugin-push --variable SENDER_ID="GCM_PROJECT_NUMBER"
    $ ionic config set gcm_key <your-gcm-project-number>
    $ ionic config set dev_push false$ ionic io init

Note: phonegap-plugin-push requires Android Support Repository version 32+

In your _app.js_ add the ‘_ionic.service.push_’ module dependency after the _ionic.service.core_

Ionic Push Code:

Initialize the service and register your device in your module's run function. You'll need the device token that is registered by the user for sending notification to the user.

  

     $ionicPush.init({  
           debug:true,
           onNotification:function (notification)   {  
              console.log'token:', notification.payload);  },  onRegister: function (token) {    console.log('      Device Token:',
              token);    $ionicPush.saveToken(token); // persist the token in the Ionic Platform
           }
        });
    $ionicPush.register();

then run

    $ grunt build:android --debug

Ionic Push lets you create targeted push notifications through the dashboard. You can also send notifications from the server in the below format.

    curl -X POST -H "Authorization:Bearer API_TOKEN" -H "Content-Type:application/json" -d '{  
       "tokens":[  
          "DEVICE_TOKEN"
       ],
       "profile":"PROFILE_TAG",
       "notification":{  
          "message":"Hello World!"      "android":{  
             "title":"Hi User",
             "message":"An update is available for your App",
             "payload":{  
                "update":true
             }
          }
       }
    }'"https://api.ionic.io/push/notifications"

Note: The steps to configure Ionic Push for iOS is the same except for creating the Push Profiles. To create iOS push profiles refer [http://docs.ionic.io/v2.0.0-beta/docs/ios-push-profiles](\"http://docs.ionic.io/v2.0.0-beta/docs/ios-push-profiles\")

* * *
