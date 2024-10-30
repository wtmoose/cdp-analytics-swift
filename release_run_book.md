# Release Run Book

## Introduction

This document outlines the steps required to create a release for the project. Since this is a fork, you can either pull upstream changes or make changes of your own.

## Steps

1. **Pull Upstream Changes or Make Own Changes**

   Decide whether you need to pull changes from the upstream repository or make your own modifications.

2. **Test in Sample Apps**

   To ensure the changes are working correctly, test them in the [sample apps](https://github.com/customerio/customerio-ios/tree/main/Apps).

   ### Swift Package Manager

   Update the `Package.swift` file of the SDK to fetch the changes you want to test
   ```swift
           .package(name: "CioAnalytics2", url: "https://github.com/customerio/cdp-analytics-swift.git", .branch("test"))
   ```

   We validate it on `APNUIKit` which uses SPM to fetch the SDK.

   ### CocoaPods

   Testing with CocoaPods is a bit tricker. You can either test it in the sample app or create a new sample app to test the changes.

   In the `Apps/CocoaPods-FCM/Podfile`

   You would notice,
   ```ruby
   pod 'CustomerIODataPipelines', :path => sdk_local_path
   ```

   `CustomerIODataPipelines` internally uses, `AnalyticsSwiftCIO` (which we need to test)

   So, we can do this by updating the `Podfile` of the sample app to include the git URL and branch name of your fork:
   ```ruby
   pod 'AnalyticsSwiftCIO', :git => "https://github.com/customerio/cdp-analytics-swift.git", :branch => 'test'
   ```

    and in the `CustomerIODataPipelines.podspec`, we need to remove the tag added to the `AnalyticsSwiftCIO` dependency.

3. Merge the changes

    Once you are satisfied with the changes, merge them into the `main` branch. Just create the PR of the `test` branch to the `main` branch.

4. Release

    Once tested and merged, we can now create a release to then later update our SDK and make it available to the customers.

    ### Steps to create a release
    - Go to `Actions` tab in the repository
    - Click on `Create Release` button
    - Select `Run Workflow`, add the tag name in the `Tag to create and deploy`
    - Format of tag we currently follow is `1.5.13+cio.1`
    - Click on `Run Workflow`

    What this action does is
    - It creates a tag with the name you provided
    - It creates a release with the tag
    - Updates podspec with the new version number
    - Publish it to the CocoaPods
