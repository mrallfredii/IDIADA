## Steps to Configure an Android Automitve Project with Android Studio
Fisrt of all, you gotta be using the programming language Java

Made by Alfred Manuel M. with Applus IDIADA
![Logo](https://upload.wikimedia.org/wikipedia/commons/7/73/Applus%2B_IDIADA_Logo.svg)

---

## Initialize Project
- Click File -> New -> New Project 
- Choose Automotive from the Templates list, and then “No Activity” 
- Click "Next"
![image](https://user-images.githubusercontent.com/31958950/205915770-a15da317-1e8a-4901-aa55-c73b1e1bc91d.png)

- Give a name to your application, I named mine "Login"
- The package name is com.example.login, but you can change it to com.companyName.nameOfYourApplication.

foto de new project [name, pack, langiuage, minimum sdk]

- Make sure you have chosen the correct Minimum SDK. I used API XX: Android X.0 (XXX)

## Setup project

Create a folder in automotive/res called xml, and add a new file automotive_app_desc.xml

![image](https://user-images.githubusercontent.com/31958950/205916984-c667c657-d086-456c-8cd1-b76362bd25c2.png)

- Paste the code below in the file:

```
<automotiveApp>  
  <uses name="template"/>  
</automotiveApp> 
```

- Open the build.gradlle (Module: automotive) file and paste the code below

```
plugins {
    alias(libs.plugins.androidApplication)
}

android {
    namespace = "com.example.login"
    compileSdk = 34

    defaultConfig {
        applicationId = "com.example.login"
        minSdk = 29
        targetSdk = 34
        versionCode = 1
        versionName = "1.0"

        testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            isMinifyEnabled = false
            proguardFiles(
                getDefaultProguardFile("proguard-android-optimize.txt"),
                "proguard-rules.pro"
            )
        }
    }
    compileOptions {
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
}
```

Explicar aixo

After changing the gradle file, you will be notified that you should sync since "Gralde files have changed since last project sync". Click “Sync now”

- Then you need Session, Screen and CarAppService - three main concepts of Android Automotive applications. Create three files in java/com-example.automotive_example: HomeScreen.kt, MyCarAppService.kt, MyCarAppSession.kt and paste the code below:

HomeScreen.java

```
package com.example.login;

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
package com.example.login;

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
package com.example.login;

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
        android:theme="@style/Theme.Login">

        <activity
            android:name=".LoginActivity"
            android:exported="true"
            android:theme="@style/Theme.Login">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <service
            android:name=".MyCarAppService"
            android:exported="true">
            <intent-filter>
                <action android:name="androidx.car.app.CarAppService"/>
                <category android:name="androidx.car.app.category.POI"/>
            </intent-filter>
        </service>

        <activity
            android:name="androidx.car.app.activity.CarAppActivity"
            android:exported="true"
            android:theme="@android:style/Theme.DeviceDefault.NoActionBar"
            android:launchMode="singleTask"
            android:label="MyCarApp">
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

