---
title: Quickstart - Add joining a teams meeting to an Android app using Azure Communication Services
description: In this quickstart, you learn how to use the Azure Communication Services Teams Embed library for Android.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
---

In this quickstart, you'll learn how to join a teams meeting using the Azure Communication Services Teams Embed library for Android.

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android Studio](https://developer.android.com/studio), for creating your Android application.
- A deployed Communication Services resource. [Create a Communication Services resource](../../create-communication-resource.md).
- A [User Access Token](../../access-tokens.md) for your Azure Communication Service.

## Setting up

### Create an Android app with an empty activity

From Android Studio, select Start a new Android Studio project.

:::image type="content" source="../media/android/studio-new-project.png" alt-text="Screenshot showing the 'Start a new Android Studio Project' button selected in Android Studio.":::

Select "Empty Activity" project template under "Phone and Tablet".

:::image type="content" source="../media/android/studio-blank-activity.png" alt-text="Screenshot showing the 'Empty Activity' option selected in the Project Template Screen.":::

Select Minimum client library of "API 21: Android 5.0 (Lollipop)" or greater.

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="Screenshot showing the 'Empty Activity' option selected in the Project Template Screen 2.":::


### Install the Azure package

Locate your project level build.gradle and make sure to add `mavenCentral()` to the list of repositories under `buildscript` and `allprojects`
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
Then, in your module level build.gradle add the following lines to the dependencies and android sections

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.azure.android:azure-communication-common:1.0.0-beta.4'
    ...
}
```

### Install the Teams Embed package

Download the `MicrosoftTeamsSDK` package here 'Insert link once posted'.

Then unzip the MicrosoftTeamsSDK folder into your projects app folder. Ex. `TeamsEmbedAndroidGettingStarted/app/MicrosoftTeamsSDK`.

### Add permissions to application manifest

In order to request permissions required to join a meeting, they must first be declared in the Application Manifest (`app/src/main/AndroidManifest.xml`). Replace the content of file with the following:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.microsoft.TeamsEmbedAndroidGettingStarted">
    <uses-permission android:name="android.permission.RECORD_AUDIO"/>
    <application
        android:name=".TeamsEmbedAndroidGettingStarted"
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme"
        tools:replace="android:name">
        <activity android:name="com.microsoft.TeamsEmbedAndroidGettingStarted.MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
</manifest>
    
```

### Update themes

Set the style name to `AppTheme` in both your `theme.xml` and `theme.xml (night)` files.

:::image type="content" source="../media/android/theme-settings.png" alt-text="Screenshot showing the theme.xml files in Android Studio":::

```xml<style name="AppTheme" parent="Theme.MaterialComponents.DayNight.DarkActionBar">```

### Set up the layout for the app

Create a button with an ID of `join_meeting`. Navigate to (`app/src/main/res/layout/activity_main.xml`) and replace the content of file with the following:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/join_meeting"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Join Meeting"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### Create application class

Create new java class file named `TeamsEmbedAndroidGettingStarted`. This will be the application class which must extend `TeamsSDKApplication`.

:::image type="content" source="../media/android/application-class-location.png" alt-text="Screenshot showing where to create application class in Android Studio":::

```java
package com.microsoft.TeamsEmbedAndroidGettingStarted;

import com.microsoft.teamssdk.app.TeamsSDKApplication;

public class TeamsEmbedAndroidGettingStarted extends TeamsSDKApplication {
}
```

### Create the main activity scaffolding and bindings

With the layout created the bindings can be added as well as the basic scaffolding of the activity. The activity will handle requesting runtime permissions, creating the meeting client, and joining a meeting when the button is pressed. Each will be covered in its own section. The `onCreate` method will be overridden to invoke `getAllPermissions` and `createAgent` as well as add the bindings for the join meeting button. This will occur only once when the activity is created. For more information on `onCreate`, see the guide [Understand the Activity Lifecycle](https://developer.android.com/guide/components/activities/activity-lifecycle).

Navigate to **MainActivity.java** and replace the content with the following code:

```java
package com.microsoft.TeamsEmbedAndroidGettingStarted;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.ui.meetings.MeetingUIClient;
import com.azure.android.communication.ui.meetings.JoinOptions;

import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {

    private final String displayName = "John Smith";

    private MeetingUIClient meetingUIClient;
    private MeetingJoinOptions meetingJoinOptions;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        meetingJoinOptions = new MeetingJoinOptions(displayName);
        
        getAllPermissions();
        createMeetingClient();

        Button joinMeeting = findViewById(R.id.join_meeting);
        joinMeeting.setOnClickListener(l -> joinMeeting());
    }

    private void createMeetingClient() {
        // See section on creating meeting client
    }

    private void joinMeeting() {
        // See section on joining a meeting
    }

    private void getAllPermissions() {
        // See section on getting permissions
    }
}
```

### Request permissions at runtime

For Android 6.0 and higher (API level 23) and `targetSdkVersion` 23 or higher, permissions are granted at runtime instead of when the app is installed. In order to support this, `getAllPermissions` can be implemented to call `ActivityCompat.checkSelfPermission` and `ActivityCompat.requestPermissions` for each required permission.

```java
/**
 * Request each required permission if the app doesn't already have it.
 */
private void getAllPermissions() {
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 202);
    }
}
```

> [!NOTE]
> When designing your app, consider when these permissions should be requested. Permissions should be requested as they are needed, not ahead of time. For more information see the [Android Permissions Guide.](https://developer.android.com/training/permissions/requesting)

## Object model

The following classes and interfaces handle some of the major features of the Azure Communication Services Teams Embed library:

| Name                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingClient| The MeetingClient is the main entry point to the Teams Embed library. |
| MeetingJoinOptions | MeetingJoinOptions are used for configurable options such as display name. |
| CallState | The CallState is used to for reporting call state changes. The options are as follows: connecting, waitingInLobby, connected, and ended. |

## Create a MeetingClient from the user access token

With the user token an authenticated meeting client can be instantiated. Generally this token will be generated from a service with authentication specific to the application. For more information on user access tokens check the [User Access Tokens](../../access-tokens.md) guide. For the quickstart, replace `<USER_ACCESS_TOKEN>` with a user access token generated for your Azure Communication Service resource.

```java
private void createMeetingClient() {
    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(tokenRefresher, true, "<USER_ACCESS_TOKEN>");
        meetingUIClient = new MeetingUIClient(credential);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## Setup Token refreshing

Create a Callable `tokenRefresher` method. Then create a fetchToken method to get the user token. [You can find instructions on how to do so here](https://docs.microsoft.com/en-us/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-java)

```java
Callable<String> tokenRefresher = () -> {
    return fetchToken();
};

public String fetchToken() {
    // Get token
    return "";
}
```

## Get the Teams meeting link

The Teams meeting link can be retrieved using Graph APIs. This is detailed in [Graph documentation](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
The Communication Services Calling SDK accepts a full Teams meeting link. This link is returned as part of the `onlineMeeting` resource, accessible under the [`joinWebUrl` property](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)
You can also get the required meeting information from the **Join Meeting** URL in the Teams meeting invite itself.

## Start a meeting using the meeting client

Joining a meeting can be done via the MeetingClient, and just requires a meetingURL and the JoinOptions. Replace `<MEETING_URL>` with a teams meeting url.

```java
/**
 * Join a meeting with a meetingURL.
 */
private void joinMeeting() {
    try {
        meetingUIClient.join("<MEETING_URL>", meetingJoinOptions);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## Launch the app and join a meeting

The app can now be launched using the "Run App" button on the toolbar (Shift+F10). 

:::image type="content" source="../media/android/quickstart-android-join-meeting.png" alt-text="Screenshot showing the completed application.":::

:::image type="content" source="../media/android/quickstart-android-joined-meeting.png" alt-text="Screenshot showing the completed application after meeting has been joined.":::

## Sample Code

You can download the sample app from [GitHub](https://github.com/Azure-Samples/teams-embed-android-getting-started)
