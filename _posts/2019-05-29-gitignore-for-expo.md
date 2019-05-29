---
layout: post
title: .expo 디렉토로도 git으로 관리해야 하나요?
tags: react-native expo
comments: true
---

리액트 네이티브를 expo로 작업할 때 .expo 디렉토리가 자동으로 생기는데 이걸 어떻게 해야 할까? git으로 관리해야 할까? 아니면 제외해야 할까?   
    
[expo에서 제공하는 .gitignore 파일](https://github.com/expo/expo/blob/master/.gitignore)을 보면 .expo 디렉토리는 **제외**하도록 되어 있다. 아래를 참고하여 expo팀은 어떤 파일을 .gitignore에 추가했는지 살펴보자.     
     
```
# Expo
.expo
__generated__

# macOS
.DS_Store

# Node
node_modules
npm-debug.log
yarn-error.log

# Emacs
*~

# Vim
.*.swp
.*.swo
.*.swn
.*.swm

# Sublime Text
*.sublime-project
*.sublime-workspace

# Xcode
*.pbxuser
!default.pbxuser
*.xccheckout
*.xcscmblueprint
xcuserdata

# Android Studio
*.iml
.gradle
.idea/libraries
.idea/workspace.xml
.idea/gradle.xml
.idea/misc.xml
.idea/modules.xml
.idea/vcs.xml

# Eclipse
.project
.settings

# Android
*.apk
*.hprof
/android/**/build
/android/**/local.properties
/android/captures
/android/expoview/libs/ReactAndroid-temp
/android/versioned-react-native/ReactAndroid
ReactAndroid-temp.aar

# Tools
jarjar-rules.txt

# Dynamic Macros
/ios/Exponent/Supporting/EXBuildConstants.plist.bak
/ios/Exponent/Supporting/EXBuildConstants.plist.json
/ios/Exponent/Supporting/EXBuildConstants.plist
/android/expoview/src/main/java/host/exp/exponent/generated/ExponentBuildConstants.java
/android/app/src/androidTest/java/host/exp/exponent/generated/TestBuildConstants.java
.kernel-ngrok-url

# Template files
/android/app/src/main/AndroidManifest.xml
/android/app/google-services.json
/android/expoview/src/main/java/host/exp/exponent/generated/ExponentKeys.java
/android/app/fabric.properties
/ios/Exponent/Generated/EXKeys.h
/ios/ExponentIntegrationTests/EXTestEnvironment.plist
/ios/Podfile
/exponent-view-template/ios/Podfile

# Codemod
.codemod.bookmark

# Fastlane
/*.cer
/fastlane/report.xml
/fastlane/Preview.html
/Deployment
/Preview.html

# CI
/android/logcat.txt

# Shell apps
android-shell-app
shellAppBase-*
shellAppIntermediates
shellAppWorkspaces
/artifacts/*

# Expo Client builds
/client-builds

# Expo web env
.env.local
.env.development.local
.env.test.local
.env.production.local
```