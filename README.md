Project Set Up

Create a parent directory for the project. This will hold both the native Android application and Flutter module as separate projects within it. Having a parent directory to the native project will make it easier to add the module to.

Now clone your native Android project into the parent directory you just created. The starter Android app used in this tutorial is available at https://github.com/connorlof/HelloWorldKotlin-starter

Once the project has been cloned you may open it in Android Studio. There is nothing special about this project. It can be cloned or downloaded then opened in Android Studio in the same way as any other native Android project.
Add Flutter Module

Open up terminal in Android Studio

cd ..

  

flutter create -t module name_of_module

  

Your Flutter module will now be at the same directory level and your native Android project.

Add compile options to the app level build.grade within the Android block if it does not already exist.

android {

​

  compileOptions {

    sourceCompatibility 1.8

    targetCompatibility 1.8

  }

  

}

Next open the settings.gradle file and add the following code to it below include ‘:app’

include ':app'

​

setBinding(new Binding([gradle:this]))

​

evaluate(new File(

  settingsDir.parentFile,

  'name_of_module/.android/include_flutter.groovy'

))

Sync gradle and this will add some additional code and files to you project to support Flutter.

Add Flutter as a dependency to your app level build.grade file.

dependencies {

​

  implementation project(':flutter')

}

Once the gradle is synced again then a Flutter module has successfully been added to the native project.
Add a Flutter Activity

There are several ways to add Flutter or Dart code to a native Android application. In this tutorial I will focus on adding a screen written completely in Flutter to an Android application. A splash screen written in Flutter will be added as a FlutterActivity to the native app. This example can be expanded on to add fully functioning Flutter screens to your native app.

Add the following code to the Activity you want to launch the FlutterActivity from. This will automatically call the main.dart file in the Flutter module and launch it as an Activity.

startActivity(FlutterActivity.createDefaultIntent(this))

I then added a Handler with a delay to launch another Activity and calling finish() to close the splash screen we have open from Flutter. The full onCreate code of our native Activity looks as follows. This change completes the native portion of this process.

override fun onCreate(savedInstanceState: Bundle?) {

  super.onCreate(savedInstanceState)

    setContentView(R.layout.activity_main)

​

    startActivity(FlutterActivity.createDefaultIntent(this))

​

    Handler().postDelayed({

    val intent = Intent(this@MainActivity,

                        HelloWorldActivity::class.java)

      startActivity(intent)

      finish()

    }, 5000)

  }

Make sure to add the FlutterActivity to the AndroidManifest.xml as follows:

<activity

    android:name="io.flutter.embedding.android.FlutterActivity"

    android:configChanges="orientation|keyboardHidden|keyboard|screenSize|locale|layoutDirection|fontScale|screenLayout|density|uiMode"

    android:hardwareAccelerated="true"

    android:windowSoftInputMode="adjustResize"

    />

Create the Flutter Splash Screen

This section goes through the process of creating the Flutter splash screen in dart. I am using a package called Flutter SpinKit to easily add a loading animation to my splash screen. It can be found at https://pub.dev/packages/flutter_spinkit and added as a dependency in pubspec.yaml

If you are familiar with Flutter you will see that the project structure of the module is very similar to a normal Flutter project. Navigate to main.dart in the lib folder. Delete the example counter app and add your code for the splash screen. My main.dart file looks as follows.

import 'dart:ui';

​

import 'package:flutter/material.dart';

import 'package:flutter_spinkit/flutter_spinkit.dart';

​

void main() => runApp(chooseWidget(window.defaultRouteName));

​

Widget chooseWidget(String route) {

  switch (route) {

    // name of the route defined in the host app

    case 'splashRoute':

      return MyFlutterView();

​

    default:

      return MyFlutterView();

  }

}

​

class MyFlutterView extends StatelessWidget {

  @override

  Widget build(BuildContext context) {

    return MaterialApp(

      debugShowCheckedModeBanner: false,

      home: SplashScreen(),

    );

  }

}

​

class SplashScreen extends StatelessWidget {

  @override

  Widget build(BuildContext context) {

    return Scaffold(

      backgroundColor: Colors.white,

      body: Container(

        child: SpinKitDoubleBounce(

          color: Colors.purple,

          size: 100.0,

        ),

      ),

    );

  }

}

​

Run Your App

Make sure the minSdk in the build.gradle of the native project and Flutter project match.

Go back into your native Android project. Run the ‘app’ configuration on your device or emulator of choice.
