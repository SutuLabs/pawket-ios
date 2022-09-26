# Put artifact at www folder
```
mkdir www
cp -rp artifact www/
```
# Install Cordova

```
npm install -g cordova
cordova platform add ios
```

# Install iOS Development Tools

[REF](https://cordova.apache.org/docs/en/11.x/guide/platforms/ios/index.html)

First Install xcode from AppStore.

For TLDR;

```sh
xcode-select --install
brew install ios-deploy
sudo gem install cocoapods
```

# Check

In the cordova project directory, run

```
cordova requirements
```

If you have successfully installed all the required tools above, the result should be like:

```
Requirements check results for ios:
Apple macOS: installed darwin
Xcode: installed 13.4.1
ios-deploy: installed 1.11.4
CocoaPods: installed 1.11.3
```

## xcode-select: error: tool 'xcodebuild' requires Xcode, but active developer directory '/Library/Developer/CommandLineTools' is a command line tools instanc

- Start Xcode.
- Open [Xcode] -> [Preferences] in the menu.
- Select Locations.
- The [Command line Tools:] list box was blank. This is the cause, so click and select the Xcode xx.x that appears.

# Build

```
cordova build ios
```

## Continue in xcode

open platforms/ios/Pawket.xcworkspace/

## Modify code

Target: /platforms/ios/CordovaLib/Classes/Public/CDVURLSchemeHandler.m

```diff
-        return contentType ? contentType : @"application/octet-stream";
+        return contentType ? contentType : [fileExtension isEqualToString:@"wasm"] ? @"application/wasm" : @"application/octet-stream";
```

Target: /platforms/ios/CordovaLib/Classes/Private/Plugins/CDVWebViewEngine/CDVWebViewEngine.m

```diff
-    wkWebView.allowsBackForwardNavigationGestures = [settings cordovaBoolSettingForKey:@"AllowBackForwardNavigationGestures" defaultValue:NO];
+    wkWebView.allowsBackForwardNavigationGestures = [settings cordovaBoolSettingForKey:@"AllowBackForwardNavigationGestures" defaultValue:YES];
```

# Signing

- https://developer.apple.com/forums/thread/47806
- https://help.apple.com/developer-account/#/devbfa00fef7

# Release

1. Modify version in `config.xml`.
2. Replace files in `platforms/ios/www/` with latest package download from [Azure Devops Pipelines](https://dev.azure.com/sututech/Chia/_build?definitionId=43&_a=summary).
    Alert: `cordova.js` and `cordova-js-src` should remain
3. Build release with `yarn build:release`.


# Incremental Release

1. Replace files in `platforms/ios/www/` with latest package download from [Azure Devops Pipelines](https://dev.azure.com/sututech/Chia/_build?definitionId=43&_a=summary).
    Alert: `cordova.js` and `cordova-js-src` should remain, copy from /www-backup if accidently deleted
2. Change version under xCode: Project Root -> General -> Targets -> Pawket -> Version/Build
3. Change target to `Any iOS Device`, then: Product -> Archive
