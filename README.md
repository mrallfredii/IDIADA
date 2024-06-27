## Steps to Setup an Android Automitve Project with Android Studio
First you gotta be using the programming language Java!

Made by Alfred Manuel M. with Applus IDIADA


![Logo](https://upload.wikimedia.org/wikipedia/commons/7/73/Applus%2B_IDIADA_Logo.svg)

---

## Prerequisites
I am using a Windows11 HP PC while creating this guide.

### Android Studio
Android Studio Jellyfish 2023.3.1

### Google Account
You also need to have a Google Account so that you can sign in on Google Play store in the emulator

## Initialize Project
- Click File -> New -> New Project 
- Choose Automotive from the Templates list, and then “No Activity” 
- Click "Next"

![image](https://user-images.githubusercontent.com/31958950/205915770-a15da317-1e8a-4901-aa55-c73b1e1bc91d.png)

- Give a name to your application, I named mine "Login_Aws"
- The package name is com.example.login_aws, but you can change it to com.companyName.nameOfYourApplication.
- Make sure you have chosen the correct Minimum SDK. I used API 29: Android 10.0 (Q)

![image](https://github.com/mrallfredii/IDIADA/blob/master/photos/New-project.png)

## Setup project

Create a folder in automotive/res called xml, and add a new file automotive_app_desc.xml

![image](https://user-images.githubusercontent.com/31958950/205916984-c667c657-d086-456c-8cd1-b76362bd25c2.png)

- Paste the code below in the file:

```
<automotiveApp>  
  <uses name="template"/>  
</automotiveApp> 
```

This add support for Android Auto to your templated app, CarAppService.

---

- Open the build.gradlle.kts (Module: automotive) file and paste the code below

```
plugins {
    alias(libs.plugins.android.application)
}

android {
    namespace = "com.example.login_aws"
    compileSdk = 34

    defaultConfig {
        applicationId = "com.example.login_aws"
        minSdk = 29
        targetSdk = 34
        versionCode = 1
        versionName = "1.0"

        testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            isMinifyEnabled = false
            proguardFiles(getDefaultProguardFile("proguard-android-optimize.txt"), "proguard-rules.pro")
        }
    }
    compileOptions {
        isCoreLibraryDesugaringEnabled = true
        sourceCompatibility = JavaVersion.VERSION_1_8
        targetCompatibility = JavaVersion.VERSION_1_8
    }
}

dependencies {

    implementation(libs.appcompat)
    implementation(libs.material)
    implementation(libs.activity)
    implementation(libs.constraintlayout)
    testImplementation(libs.junit)
    androidTestImplementation(libs.ext.junit)
    androidTestImplementation(libs.espresso.core)
    implementation(libs.carAppAutomotive)
    coreLibraryDesugaring(libs.desugar)
    implementation(libs.amplifyAuthenticator)
    implementation(libs.retrofit)
    implementation(libs.awsAndroidSdkCore)
    implementation(libs.awsAndroidSdkAuthUi)
    implementation(libs.awsAndroidSdkAuthUserpools)
    implementation(libs.awsAndroidSdkCognitoIdentityProvider)
    implementation(libs.retrofitConverterGson)
}
```

This is a build file of your specific module (where you add your dependencies, signing configurations, build types, flavors, etc.)


In the file libs.versions.toml is where is defiend the versions of these dependencies:
```
[versions]
agp = "8.4.2"
junit = "4.13.2"
junitVersion = "1.1.5"
espressoCore = "3.5.1"
appcompat = "1.7.0"
material = "1.12.0"
activity = "1.9.0"
constraintlayout = "2.1.4"
carAppAutomotive = "1.7.0-alpha02"
desugar = "2.0.4"
amplifyAuthenticator = "1.2.0"
retrofit = "2.9.0"
retrofitConverterGson = "2.9.0"
awsSdk = "2.57.0"

[libraries]
junit = { group = "junit", name = "junit", version.ref = "junit" }
ext-junit = { group = "androidx.test.ext", name = "junit", version.ref = "junitVersion" }
espresso-core = { group = "androidx.test.espresso", name = "espresso-core", version.ref = "espressoCore" }
appcompat = { group = "androidx.appcompat", name = "appcompat", version.ref = "appcompat" }
material = { group = "com.google.android.material", name = "material", version.ref = "material" }
activity = { group = "androidx.activity", name = "activity", version.ref = "activity" }
constraintlayout = { group = "androidx.constraintlayout", name = "constraintlayout", version.ref = "constraintlayout" }
carAppAutomotive = { group = "androidx.car.app", name = "app-automotive", version.ref = "carAppAutomotive" }
desugar = { group = "com.android.tools", name = "desugar_jdk_libs", version.ref = "desugar" }
amplifyAuthenticator = { group = "com.amplifyframework.ui", name = "authenticator", version.ref = "amplifyAuthenticator" }
retrofit = { group = "com.squareup.retrofit2", name = "retrofit", version.ref = "retrofit" }
retrofitConverterGson = { group = "com.squareup.retrofit2", name = "converter-gson", version.ref = "retrofitConverterGson" }
awsAndroidSdkCore = { group = "com.amazonaws", name = "aws-android-sdk-core", version.ref = "awsSdk" }
awsAndroidSdkCognitoIdentityProvider = { group = "com.amazonaws", name = "aws-android-sdk-cognitoidentityprovider", version.ref = "awsSdk" }
awsAndroidSdkAuthUserpools = { group = "com.amazonaws", name = "aws-android-sdk-auth-userpools", version.ref = "awsSdk" }
awsAndroidSdkAuthUi = { group = "com.amazonaws", name = "aws-android-sdk-auth-ui", version.ref = "awsSdk" }

[plugins]
android-application = { id = "com.android.application", version.ref = "agp" }

```

After changing the gradle file, you will be notified that you should sync since "Gralde files have changed since last project sync". Click “Sync now”

---

- Then you need Session, Screen and CarAppService - three main concepts of Android Automotive applications. Create three files in java/com.example.login_aws: HomeScreen.java, MyCarAppService.java, MyCarAppSession.java and paste the code below for doing a Hello World:

HomeScreen.java

```
package com.example.login_aws;

import androidx.car.app.CarContext;
import androidx.car.app.Screen;
import androidx.car.app.model.Action;
import androidx.car.app.model.Pane;
import androidx.car.app.model.PaneTemplate;
import androidx.car.app.model.Row;
import androidx.car.app.model.Template;

public class HomeScreen extends Screen {

    public HomeScreen(CarContext carContext) {
        super(carContext);
    }

    @Override
    public Template onGetTemplate() {
        Row row = new Row.Builder()
                .setTitle("Hello World!")
                .build();

        Pane pane = new Pane.Builder()
                .addRow(row)
                .build();

        return new PaneTemplate.Builder(pane)
                .setHeaderAction(Action.APP_ICON)
                .build();
    }
}
```
HomeScreen extends Screen and represents the UI displayed in the car app

MyCarAppService.java

```
package com.example.login_aws;

import android.content.pm.ApplicationInfo;
import androidx.car.app.CarAppService;
import androidx.car.app.Session;
import androidx.car.app.validation.HostValidator;

public class MyCarAppService extends CarAppService {

    @Override
    public Session onCreateSession() {
        return new MyCarAppSession();
    }

    @Override
    public HostValidator createHostValidator() {
        if ((getApplicationInfo().flags & ApplicationInfo.FLAG_DEBUGGABLE) != 0) {
            return HostValidator.ALLOW_ALL_HOSTS_VALIDATOR;
        } else {
            return new HostValidator.Builder(getApplicationContext())
                    .addAllowedHosts(androidx.car.app.R.array.hosts_allowlist_sample)
                    .build();
        }
    }
}
```

MyCarAppService extends CarAppService and manages the lifecycle of the car app

MyCarAppSession.java

```
package com.example.login_aws;

import static android.content.Context.MODE_PRIVATE;

import android.content.Intent;
import android.content.SharedPreferences;
import androidx.car.app.Screen;
import androidx.car.app.Session;
import androidx.car.app.CarContext;

public class MyCarAppSession extends Session {

    @Override
    public Screen onCreateScreen(Intent intent) {
        SharedPreferences prefs = getCarContext().getSharedPreferences("loginPrefs", MODE_PRIVATE);
        boolean isLoggedIn = prefs.getBoolean("is_logged_in", false);

        if (isLoggedIn) {
            // If the user is logged in, return the HomeScreen
            return new HomeScreen(getCarContext());
        } else {
            // If the user is not logged in, return null
            return null;
        }
    }
}
```

MyCarAppSession extends Session and handles the creation of the initial screen.

- In the documentation you can read more about why you need Screen, Session and CarAppService: https://developer.android.com/training/cars/apps#key-terms-concepts

---

- Open the automotive/src/main/AndroidManifest.xml file and paste in the code below

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <uses-feature
        android:name="android.hardware.type.automotive"
        android:required="true" />

    <uses-feature
        android:name="android.software.car.templates_host"
        android:required="true" />
    <uses-feature
        android:name="android.hardware.wifi"
        android:required="false" />
    <uses-feature
        android:name="android.hardware.screen.portrait"
        android:required="false" />
    <uses-feature
        android:name="android.hardware.screen.landscape"
        android:required="false" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        >

        <service
            android:name=".MyCarAppService"
            android:exported="true">
            <intent-filter>
                <action android:name="androidx.car.app.CarAppService"/>
                <category android:name="androidx.car.app.category.POI"/>
            </intent-filter>
        </service>

        <activity
            android:exported="true"
            android:theme="@android:style/Theme.DeviceDefault.NoActionBar"
            android:name="androidx.car.app.activity.CarAppActivity"
            android:launchMode="singleTask"
            android:label="IDIADA">


            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>

            <meta-data android:name="distractionOptimized" android:value="true" />

        </activity>
        <meta-data android:name="com.android.automotive"
            android:resource="@xml/automotive_app_desc"/>
        <meta-data
            android:name="androidx.car.app.minCarApiLevel"
            android:value="1"/>

    </application>

</manifest>
```

In ```<category android:name="androidx.car.app.category.POI"/>``` the category of the application is sat. The supported categories you can choose between is provided here: https://developer.android.com/training/cars#supported-app-categories. Set your desired cateogry after "androidx.car.app.category."

