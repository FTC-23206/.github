---
title: "Setup"
permalink: /pond/setup
toc: true
---

## Setup Options

There are 2 available setup methods:

* **Quick-Start Repository**: zero-code process that gets you started from a new FTC Repo which is ready to run. This is recommended for beginner to intermediate teams which are not looking into bringing their existing code *as-is* to the new repository.

* **In-Place Update**: add Pond library reference to your existing code. This is recommended for advanced teams who wants to be selective about how to integrate and which functionality to depend on.

> **Recommendation**: The *Quick-Start Repository* is the recommended way to get started as it is the fastest option and allows you to quickly evaluate the library functionality with little to no effort. Additionally, setting up a new repository allows you to evaluate the *Pond* functionality without making any changes to your current robot project.
>
> In any case, the 2 setup methods allow you to reach the same result as the Pond library is always used as a library reference. The only difference is the path to get there.
{: .notice--info}

## Option 1: Quick-Start Repository

### Fork the Repository (skip if evaluating only)

This step shows how to [fork][GithubFork] the starter repository in your own [Github] account or team organization. This is recommended if you are planning to use the started repository as the base for your team code.

> You can skip this step if you only want to copy the code locally on your machine (or if you still don't have a github account)
{: .notice}

1. Sign-in to [Github](https://github.com/){:target="_blank" rel="noopener"}
2. Open <https://github.com/FTC-23206/automaducks-pond-starter>{:target="_blank" rel="noopener"}
3. Click on *Fork* and provide the requested information (make sure you are making the fork to your Team organization or your own account)

    <img src="/images/pond/pond-github-starter-fork.png" width="600" style="display: block; margin: 0 auto;" alt="Shows github fork button" />

4. Clone your own repository so you can continue to make changes to your robot project.

### Clone the Repository

This step downloads the code to your machine and allow you to create the FTC [Android Studio] project.

**From Android Studio**

1. Install *Android Studio* from <https://developer.android.com/studio>{:target="_blank" rel="noopener"}
2. Open *Android Studio* and choose 'Clone Repository'
    <img src="/images/pond/pond-android-clone.png" width="600" style="display: block; margin: 0 auto;" alt="" />
3. Give the repository URL:
    1. Give the URL of your forked repository.
    2. If you've decided to to skip the fork step, use `https://github.com/FTC-23206/automaducks-pond-starter.git`

**From Command Line (Advanced)**

1. Install *git* from <https://git-scm.com/downloads>{:target="_blank" rel="noopener"}
2. Open `Command Prompt` or `Terminal` and navigate to the desired repository location
3. Clone the [Pond-Starter](https://github.com/FTC-23206/automaducks-pond-starter){:target="_blank" rel="noopener"} repository:

    1. If you forked the repository:

        ```powershell
        git clone URL_TO_YOUR_FORKED_REPOSITORY
        ```

    1. If you skipped forking the repository:

        ```powershell
        git clone https://github.com/FTC-23206/automaducks-pond-starter.git
        ```

4. Open the the root of the repository with *Android Studio*

### Build the Project

At this point you should be able to successfully build the project:

<img src="/images/pond/pond-android-build.png" width="600" style="display: block; margin: 0 auto;" alt="Android Studio Build" />

1. Shows the project files
2. Click on the 'Make' button
3. Once it completes, it should show a successful build operation.

Now learn how to run and modify the code on the [Getting Started](/pond/getting-started) page.

## Option 2: In-Place Update

### Add the Pond package reference (Required)

1. Make sure your `TeamCode` project successfully builds in Android Studio (`F7`)
2. Add the following to your Team Code gradle file
    File: `(RepositoryRoot)\TeamCode\build.gradle`

    ```gradle
    dependencies {
        implementation project(':FtcRobotController')

        // Pond Library Reference
        implementation 'com.automaducks:pond:0.9.0'
    }
    ```

3. Sync the project with gradle files (`CTRL+SHIFT+O`)
4. Rebuild `TemCode` project (`F7`)

After a successful build, you are ready to start leveraging the *Pond* libraries.

### Add FTC Dashboard and Road Runner (Recommended)

This is not require but Pond is made to work well with FTC dashboard as it can leverage dynamic configuration changes and graphing.

1. Similar to the above, update your Team Code `build.gradle` per below:

    ```gradle
    //
    // build.gradle in TeamCode
    //
    // Most of the definitions for building your module reside in a common, shared
    // file 'build.common.gradle'. Being factored in this way makes it easier to
    // integrate updates to the FTC into your code. If you really need to customize
    // the build definitions, you can place those customizations in this file, but
    // please think carefully as to whether such customizations are really necessary
    // before doing so.


    // Custom definitions may go here

    // Include common definitions from above.
    apply from: '../build.common.gradle'
    apply from: '../build.dependencies.gradle'

    android {
        namespace = 'org.firstinspires.ftc.teamcode'

        packagingOptions {
            jniLibs.useLegacyPackaging true
        }
    }

    repositories {
        maven {
            url = 'https://maven.brott.dev/'
        }
    }

    dependencies {
        implementation project(':FtcRobotController')

        // Pond
        implementation 'com.automaducks:pond:0.9.0'

        // ACME Libraries
        implementation 'com.acmerobotics.roadrunner:core:1.0.1'
        implementation 'com.acmerobotics.dashboard:dashboard:0.4.16'
    }
    ```

2. Sync the project with gradle files (`CTRL+SHIFT+O`)
3. Rebuild `TemCode` project (`F7`)

### Integrating with Pond

Continue to [Pond Integration](/pond/integration) for information on how to integrate the pond library on your project.

[Github]: <https://www.github.com> "Github"
[GithubFork]: <https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo> "Github Fork"
[Android Studio]: <https://developer.android.com/studio> "Android Studio"

