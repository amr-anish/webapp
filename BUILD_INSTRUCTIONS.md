# Tamilian TV Android APK - Build Instructions

## ⚠️ Current Status
The APK build encountered technical issues with the current build environment. The generated APKs are too small (13-45KB) and will show "Package parsing error" because they're missing essential components.

## 🔧 Issues Encountered
1. **Java/DEX compilation errors** - Build tools incompatibility with Java versions
2. **Missing classes.dex** - No executable code in the APK
3. **Resource-only APK** - APK contains only resources, not a complete app

## 🏗️ Proper Build Requirements

### Option 1: Android Studio (Recommended)
1. Install Android Studio
2. Import the project from the `app/` directory
3. Set up Android TV SDK (API 21-34)
4. Build → Generate Signed Bundle/APK
5. Choose APK, create keystore, build release

### Option 2: Command Line (Fixed)
```bash
# 1. Compile resources
aapt package -f -m \
  -S app/src/main/res \
  -M app/src/main/AndroidManifest.xml \
  -I $ANDROID_HOME/platforms/android-34/android.jar \
  -F build/tamilian-tv.apk \
  -J build/gen

# 2. Compile Java with correct version
javac -cp $ANDROID_HOME/platforms/android-34/android.jar \
  -sourcepath build/gen \
  -d build/classes \
  app/src/main/java/com/tamilian/tv/MainActivity.java \
  build/gen/com/tamilian/tv/R.java

# 3. Convert to DEX (use older dx tool)
dx --dex --output=build/classes.dex build/classes/

# 4. Add DEX to APK
aapt add build/tamilian-tv.apk build/classes.dex

# 5. Sign APK
apksigner sign --ks keystore.jks build/tamilian-tv.apk
```

### Option 3: Gradle Build
```gradle
// app/build.gradle
apply plugin: 'com.android.application'

android {
    compileSdkVersion 34
    defaultConfig {
        applicationId "com.tamilian.tv"
        minSdkVersion 21
        targetSdkVersion 34
        versionCode 1
        versionName "1.0"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt')
        }
    }
}

dependencies {
    implementation 'androidx.leanback:leanback:1.0.0'
}
```

## 📱 Expected Final APK
- **Size**: 2-5 MB (not KB!)
- **Contains**: classes.dex, resources, manifest, META-INF
- **Works on**: Android TV, Fire TV, TV boxes

## 🔄 Alternative Solutions

### Quick Fix: Use Web App
Create a simple HTML file that redirects to tamilian.io:
```html
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="refresh" content="0; url=https://tamilian.io/">
    <title>Tamilian TV</title>
</head>
<body>
    <p>Redirecting to <a href="https://tamilian.io/">Tamilian.io</a></p>
</body>
</html>
```

### Use Existing Browser
1. Install Chrome/Firefox on Android TV
2. Bookmark https://tamilian.io/
3. Set to landscape mode
4. Use TV remote for navigation

## 📂 Project Structure
```
app/
├── src/main/
│   ├── AndroidManifest.xml ✅
│   ├── java/com/tamilian/tv/
│   │   └── MainActivity.java ✅
│   └── res/
│       ├── layout/activity_main.xml ✅
│       ├── values/
│       │   ├── strings.xml ✅
│       │   ├── colors.xml ✅
│       │   └── themes.xml ✅
│       ├── drawable/app_banner.xml ✅
│       └── mipmap-*/ic_launcher.xml ✅
└── build.gradle ❌ (needed)
```

## 🎯 Next Steps
1. Set up proper Android development environment
2. Use Android Studio for reliable builds
3. Test on actual Android TV device
4. Upload working APK to GitHub releases

The source code is complete and correct - only the build process needs proper tooling.