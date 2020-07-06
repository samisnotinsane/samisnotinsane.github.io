---
layout: post
title:  "Geolocation and mapping in apps"
date:   2020-07-05 23:55:00 +0100
categories: Mobile, App, Flutter
---

I came across Flutter during the lockdown this year while working on a hobby project where I needed a mobile app with native functionality. Having suffered trauma from building UI using Java Swing and FX before, I initially had little enthusiasm for the project at hand. 

I have never been so wrong.

The declarative style of Flutter combined with the straightforward but familiar syntax of Dart is a marvel. I've had so many "is that it?!" moments, that I've basically lost track - it's simply a joy to use such a powerful framework.

# Final Result
Today, we will look at how we can build something like the Uber home screen with a map.

<div style="text-align: center">
    <img src="/assets/flutter-uber-mock-screenshot.png" width="200" />
</div>
<br />

# Steps
First, let's break the task down by visual features, going from top to bottom. We will need a:

1. Map with a custom colour scheme centered on device current location
2. Card which takes up roughly half of screen length.
3. User greeting and a 'Where to?' box.
4. Scrollable list of destination an icon, title and subtitle.

Note: I encourage you to code and follow along but as a reminder all code is available in my [geo_maps](https://github.com/samisnotinsane/flutter-bites/tree/master/geo_maps) repo.

# Map with custom theme
Let's integrate Google Maps into our app. We will need an API key, a package dependency and of course, the app skeleton on which the map will be displayed.

Open the Google Cloud Platform console and navigate to credentials where you will be able to create a new API key. Once done, you should be able to see it under the 'API Keys' table (see screenshot below).

<div style="text-align: center">
    <img src="/assets/google-console-api.png" width="200" />
</div>
<br />

Caution: It's advisable not to share live API keys publicly as others using your key may incur charges on your behalf.

With that out of the way, we now have to activate the Maps SDK so our app will be able to query Google's map servers. Since Flutter is cross platform, your app could potentialy run on both iOS and Android, so we'll activate both 'Maps SDK for iOS' and 'Maps SDK for Android'; just use the search bar to find these two products and click on 'Enable' under their respective pages to activate.

#### Project Configuration and Adding Dependencies
[pub.dev](https://pub.dev/) is *the* place for all your package needs. If you're coming from JavaScript world, this is akin to NPM. From pub.dev, we need a package called [google_maps_flutter](https://pub.dev/packages/google_maps_flutter) (version `^0.5.28+1` as of writing) which does exactly what it says on the tin

Add this in your `pubspec.yaml` like so:

````
dependencies:
  ...
  google_maps_flutter: ^0.5.28+1
````

Note: `yaml` files are notorious for being sensitive to indentation, so pay extra attention here.

Now copy and paste the following line with your API key in your Android application manifest (located in `android/app/src/main/AndroidManifest.xml`):

````
<meta-data android:name="com.google.android.geo.API_KEY"
               android:value="YOUR KEY HERE"/>
````

Be sure to place it as the direct child of the `<application>` tag.

That's all for Android, now on to iOS.

Open `ios/Runner/AppDelegate.swift` and replace the file contents with the following code block (but add in your API key of course):

````
import UIKit
import Flutter
import GoogleMaps

@UIApplicationMain
@objc class AppDelegate: FlutterAppDelegate {
  override func application(
    _ application: UIApplication,
    didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
  ) -> Bool {
    GMSServices.provideAPIKey("YOUR KEY HERE")
    GeneratedPluginRegistrant.register(with: self)
    return super.application(application, didFinishLaunchingWithOptions: launchOptions)
  }
}
````
One last thing in this step: the custom theme. Just use the [Google Maps Styling Wizard](https://mapstyle.withgoogle.com/) to create or select a predefined theme and copy the generated JSON.

In our case, since we're looking for an 'Uber-esque' theme, we're going to use [this link](https://snazzymaps.com/style/90982/uber-2017) to copy a similarly structured JSON with different values.

Create a new folder `/assets` in project root and create a JSON file `map_style.json` (it doesn't matter what you name it), pasting in the JSON string you copied from the link above.

Now you have to give your app permission to access this newly created `/assets` folder. Open `pubspec.yaml` and scroll down, you should see a commented out section about assets. Uncomment and change so it reads the following:

````
  assets:
     - assets/
````

This gives your app access to all files under `assets` folder, including `map_style.json` that you just created.

We will come back to this later after creating a skeleton app, so we can apply our style on the rendered map.

**Lost?** See [this](https://github.com/samisnotinsane/flutter-bites/commit/800a1512778d4c061128c88f6aae3ccad75a947b#diff-ef3842c19e4a6b4139f27c2313c9c4b4) and [this](https://github.com/samisnotinsane/flutter-bites/commit/bd088d4d4e0aa81c9f6224ddf6b854637340ddb3#diff-ef3842c19e4a6b4139f27c2313c9c4b4) example to get back on track!

# Acquiring Device Location
Remember how we added a dependency in `pubspec.yaml` earlier? We're going to add another one now called [geolocator](https://pub.dev/packages/geolocator) (version `^5.3.2+2` as of writing) which will determine device location.

Add this in your dependency list:
`geolocator: ^5.3.2+2`

Since device location is a sensitive operation, we need to ask the app user for permission. In iOS, this is configured by adding the following keys in `Info.plist` file (these should be the direct child of the `<dict>` tag):

````
<key>NSLocationWhenInUseUsageDescription</key>
<string>This app needs access to location when open.</string>

<key>NSLocationAlwaysUsageDescription</key>
<string>This app needs access to location when in the background.</string>

<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>This app needs access to location when open and in the background.</string>
````

In Android's case, open `android/app/src/main/AndroidManifest.xml` and add the following line as a direct child of the top-level `<manifest>` tag:

````
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
````

**Lost?** See [example](https://github.com/samisnotinsane/flutter-bites/commit/60ddadb0ad026c244099c39c21616bee4ed9e905#diff-ef3842c19e4a6b4139f27c2313c9c4b4) to get back on track!

# Constructing App Layout
Now for the fun part! We begin with a blank slate:

`lib/main.dart`
````
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Geo Maps',
      theme: ThemeData(
        accentColor: Color(0xFFFF6238), // Orange, opacity=1.0
        visualDensity: VisualDensity.adaptivePlatformDensity,
      ),
      home: Scaffold(
        body: Placeholder(),
      ),
    );
  }
}
````
