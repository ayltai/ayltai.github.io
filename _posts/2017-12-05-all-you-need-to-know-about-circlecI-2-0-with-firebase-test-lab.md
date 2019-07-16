---
layout     : post
author     : Alan Tai
date       : 2017-12-05
title      : All you need to know about CircleCI 2.0 with Firebase Test Lab
subtitle   : Learn how to run super fast Android tests on CircleCI 2.0 & Firebase Test Lab
image      : posts/2017/12/06/meme.jpg
categories : Pipeline
---
## Problem with CircleCI 1.0

For Android developers, the one major issue with CircleCI 1.0 is speed. Don’t get me wrong. I enjoyed the free services offered by CircleCI. And from my experience, it is not slower than its competitors. But it is definitely slower than my personal computer.

I have tried to speed up my Android builds with the help of the [official tips](https://circleci.com/blog/why-is-my-build-slow/). No luck. For every build, we have to download the latest Android tools and dependencies. This process is slow in the most of the time, and sometimes the network connection of your container randomly drops, breaking your builds. It does reduce a bit the build time with the help of [parallelism](https://circleci.com/docs/1.0/parallelism/), but still at least 50% of the build time are wasted for downloading Android dependencies.

## Solution

CircleCI 2.0 comes with many great features that promise to solve the problems with its predecessor.

Native docker support: We no longer have to wait to download Android dependencies for each build. CircleCI offers pre-configured Docker images with the latest Android tools installed. You can even use a custom image if you need to.

Workflow support: [Workflow](https://circleci.com/docs/2.0/workflows/) is similar to [parallelism](https://circleci.com/docs/1.0/parallelism/), but better. You define sub-tasks of your build task, tell CircleCI the dependencies between these tasks, and it will run all independent tasks in parallel and as soon as possible, as long as idle containers are available.

Yet, CircleCI 2.0 solves only part of the problem that Android developers complain.

## What about instrumented testing?

Running instrumented tests on emulators in CircleCI 1.0 containers is known to be painfully slow. What’s worse, it is the same for CircleCI 2.0. It is because x86 emulators are not supported on CircleCI, and running ARM emulators is way too slow to be useful. [**Firebase Test Lab**](https://firebase.google.com/docs/test-lab/) offers both real and emulated devices for running instrumented tests but it could be hard to integrate CircleCI with Firebase Test Lab because of the lack of detailed documentation.

The [official integration guide](https://firebase.google.com/docs/test-lab/continuous) from Firebase is for Jenkins CI only, while the the [guide](https://circleci.com/docs/1.0/firebase-test-lab/) from CircleCI is for 1.0. Here I will show you how to run fast Android tests on Firebase Test Lab with CircleCI 2.0 with an [working example on GitHub](https://github.com/ayltai/Newspaper).

## Getting started

From my experience, it is easier to create a CircleCI 2.0 script from scratch and iterate than to migrate from an existing CircleCI 1.0 script. As a prerequisite, you should be able to run this [minimal CircleCI 2.0 Android build script](https://circleci.com/docs/2.0/language-android/) for your project.

First, let’s define what tasks we need to do build fast on CircleCI. Here is the skeleton of our build script:

```yml
version: 2references:
  # We will define reusable references herejobs:  # Build debug APK for unit tests and an instrumented test APK
  build_debug:
    # ...  # Build release APK
  build_release:
    # ...  # Run unit tests
  test_unit:
    # ...  # Run instrumented tests
  test_instrumented:
    # ...  # Submit JaCoCo coverage report
  report_coverage:
    # ...  # Deploy release APK
  deploy:
    # ...workflows:
  version: 2
  workflow:
    jobs:
      - build_debug
      - build_release
      - test_unit
      - test_instrumented:
          requires:
            - build_debug
      - report_coverage:
          requires:
            - test_unit
            - test_instrumented
      - deploy:
          filters:
            branches:
              only:
                - master
          requires:
            - build_release
            - test_unit
            - test_instrumented
```

* `references`: In this section, we will define some useful constants that will be referenced repeatedly, such as the cache key and the workspace paths.

* `build_debug`: This is to run the Gradle tasks, `assembleDebug` and `assembleAndroidTest`. Since `assembleAndroidTest` depends on `assembleDebug`, it is generally a good idea to run them together so that we can save the time spent on preparing the task dependencies. We will store the task dependencies to cache for later use by dependent tasks. The output APKs will be saved to the workspace shared by all tasks.

* `build_release`: This is for running `assembleRelease`, which usually takes a lot more time than `assembleDebug`. I suggest to run it as a separate task because your release build may fail due to ProGuard mis-configuration while the debug build succeeds.

* `test_unit`: Run unit tests after we have our debug APK built.

* `test_instrumented`: Run instrumented test on Firebase Test Lab.

* `report_coverage`: This is an optional but recommended task to generate a test coverage report.

* `deploy`: Finally, when all tasks run successfully, we may want to deploy our release build.

The workflow of the above tasks is illustrated below. Tasks, `build_debug`, `build_release` and `test_unit`, are executed in parallel, while the rest of the tasks will run as soon as they are ready.

![The suggested Android build workflow](/assets/img/posts/2017/12/06/workflow.png "The suggested Android build workflow")

I will use my [open source project](https://github.com/ayltai/Newspaper) as an example. The complete YAML file is available [here](https://github.com/ayltai/Newspaper/blob/master/.circleci/config.yml).

## Gradle build tasks

We need to assemble at least two builds, debug and release. Some may need builds of different flavors but the task definition is similar. The snippet below shows you how to define a task for `assembleDebug`.

```yml
build_debug:
  <<: *android_config
  steps:
    - checkout
    - *restore_cache
    - run:
        name: Download dependencies
        command: ./gradlew androidDependencies
    - *save_cache
    - *export_gservices_key
    - *decode_gservices_key
    - run:
        name: Gradle build (debug)
        command: ./gradlew -PciBuild=true :app:assembleDebug :app:assembleAndroidTest
    - *persist_debug_workspace
    - store_artifacts:
        path: app/build/outputs/apk/
        destination: /apk/
```

### Container environment setup

```yml
<<: *android_config
```

The first thing a task does is to define what Docker image the task will run on. As we will use the same Docker image for different tasks, it is better to define the image configurations in `references` section.

```yml
android_config: &android_config
  working_directory: *workspace
  docker:
    - image: circleci/android:api-27-alpha
  environment:
    TERM: dumb
    _JAVA_OPTIONS: "-Xmx2048m -XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap"
    GRADLE_OPTS: '-Dorg.gradle.jvmargs="-Xmx2048m"'
```

`&android_config` refers to the [publicly available Docker image](https://hub.docker.com/r/circleci/android/tags/) provided by CircleCI. It has the latest Android tools and dependencies installed so this is a great time saver.

*Tip 1: Use a `dumb` terminal to avoid weird output from Gradle.*

*Tip 2: Explicitly limit the JVM heap size to prevent your container from running out of memory. For free users, each container has 4GB memory available.*

### Cache project dependencies

```yml
- checkout
- *restore_cache
- run:
    name: Download dependencies
    command: ./gradlew androidDependencies
- *save_cache
```

We run `androidDependencies` task to download, if not cached, all project dependencies and save them to cache, shared by all containers. A typical cache key should look like this:

```yml
{% raw %}cache-{{ checksum "gradle/wrapper/gradle-wrapper.properties" }}-{{ checksum "build.gradle" }}-{{ checksum "app/build.gradle" }}{% endraw %}
```

The value of the cache key should change if any of the project dependencies changes.

### Load secret keys dynamically

```yml
- *export_gservices_key
- *decode_gservices_key
```

One should never commit any secret keys to a public Git repository. Instead, you should store your encoded keys as CircleCI environment variables and decode them in your container when needed.

To export CircleCI environment variables to the bash shell of your container:

```yml
echo 'export GOOGLE_SERVICES_KEY="$GOOGLE_SERVICES_KEY"' >> $BASH_ENV
```

To decode Google Services key using Base64:

```yml
echo $GOOGLE_SERVICES_KEY | base64 -di > app/google-services.json
```

### Build the APKs

```yml
./gradlew -PciBuild=true assembleDebug assembleAndroidTest
```

In this step, we need to build two APKs, one debug build for unit testing and one for Espresso instrumented testing.

*Tip: Define a Gradle build variable in your build.gradle to control the build target SDK version. For building on a local machine, we can set the target SDK version to 21 so that the build time can be greatly reduced. For building on CI, we will want to set it to our lowest supported SDK version, say API 16.*

### Persist workspace

```yml
- *persist_debug_workspace
```

For debug build, only the output APKs are needed in later stages. For release build, we need to persist more files. For instance, `mapping.txt` generated by ProGuard is required when we upload the APK to [Crashlytics Beta](http://try.crashlytics.com/beta/) in the deploy task. So we simply persist the entire `/build` directory.

### Store artifacts

```yml
- store_artifacts:
    path: app/build/outputs/apk/
    destination: /apk/
```

This step is optional but highly recommended. We store the output APKs as CircleCI artifacts so that we can download them from CircleCI when the task completes. This is handy for other developers in your team to test your APK without building it themselves or asking you to send it to them. Moreover, it is generally recommended to build APKs on a dedicated environment instead of the developer’s machines, which could be unstable and tend to change frequently.

### Running tests

We will skip `test_unit` task as it is almost identical to `build_debug` task. The main focus here is how to run instrumented tests on Firebase Test Lab through CircleCI.

```yml
test_instrumented:
  <<: *gcloud_config
  steps:
    - *attach_debug_workspace
    - *export_gcloud_key
    - *decode_gcloud_key
    - run:
        name: Set Google Cloud target project
        command: gcloud config set project newspaper-84169
    - run:
        name: Authenticate with Google Cloud
        command: gcloud auth activate-service-account firebase-adminsdk-p9qvk@newspaper-84169.iam.gserviceaccount.com --key-file ${HOME}/client-secret.json
    - run:
        name: Run instrumented test on Firebase Test Lab
        command: gcloud firebase test android run --type instrumentation --app app/build/outputs/apk/debug/app-debug.apk --test app/build/outputs/apk/androidTest/debug/app-debug-androidTest.apk --device model=Nexus5X,version=26,locale=en_US,orientation=portrait --environment-variables coverage=true,coverageFile=/sdcard/tmp/code-coverage/connected/coverage.ec --directories-to-pull=/sdcard/tmp --timeout 20m
    - run:
        name: Create directory to store test results
        command: mkdir firebase
    - run:
        name: Download instrumented test results from Firebase Test Lab
        command: gsutil -m cp -r -U "`gsutil ls gs://test-lab-3udbiqpdyp0d0-miwcp7d69v80m | tail -1`*" /root/workspace/firebase/
    - *persist_firebase_workspace
    - store_artifacts:
        path: firebase/
        destination: /firebase/
```

### Build environment for Firebase Test Lab

We will need to use `gcloud` command to run tests on Firebase Test Lab. Unfortunately, `gcloud` command is not available in the CircleCI Android Docker image we used earlier. What’s worse, the Android Docker image is unable to install `gcloud` command. So we must use a different image and mount the workspace shared by `build_debug` task. The recommended image that supports gcloud is of course the official Google Cloud Docker image.

```yml
gcloud_config: &gcloud_config
  working_directory: *workspace
  docker:
    - image: google/cloud-sdk:latest
  environment:
    TERM: dumb
```

### Load Google Cloud Service Account key dynamically

A [service account](https://console.firebase.google.com/u/0/project/_/settings/serviceaccounts/adminsdk) key is required for running tests on Firebase Test Lab from CircleCI. You have to create one if it is not already created, download the secret key, (Base64) encode it, and save it as a CircleCI environment variable. We will (Base64) decode it before we use `gcloud` command.

```sh
echo $GCLOUD_SERVICE_KEY | base64 -di > ${HOME}/client-secret.json
```

### Configure your Firebase Test Lab project

We need to set the default project on Firebase for every build because CircleCI provides us a clean environment to work on:

```sh
gcloud config set project <your Firebase project ID, e.g. newspaper-84169>
```

The we have to use our secret key to activate the service account:

```sh
gcloud auth activate-service-account <your Firebase service account ID> --key-file ${HOME}/client-secret.json
```

Now, our gcloud command is authorized and we are ready to upload our APKs and run tests on Firebase Test Lab:

```sh
gcloud firebase test android run \
  --type instrumentation \
  --app app/build/outputs/apk/debug/app-debug.apk \
  --test app/build/outputs/apk/androidTest/debug/app-debug-androidTest.apk \
  --device model=Nexus5X,\
  version=26,\
  locale=en_US,\
  orientation=portrait \
  --environment-variables coverage=true,\
  coverageFile=/sdcard/tmp/code-coverage/connected/coverage.ec \
  --directories-to-pull=/sdcard/tmp \
  --timeout 20m
```

* `type instrumentation`: We want to run Espresso instrumented tests.

* `app`: This is our debug build APK generated by build_debug task.

* `test`: This is our instrumented test APK, also generated by `build_debug` task.

* `device`: I use a real device, Nexus 5X, in this example. You may use an emulator if you want.

* `environment-variables`: This is necessary if we want to generate a test coverage report from a device on Firebase Test Lab. This will set the environment variables for our Android device. Setting `coverage=true` will enable test coverage report generation when the tests complete. The test coverage report will be written to the path specified by `coverageFile`.

* `directories-to-pull`: The test coverage report can only be written to a path of the device, so we have to copy the report file to the artifacts directory of Firebase Test Lab.

* `timeout`: It is generally a good idea to limit the test execution time. If you are on a paid plan, it is unwise to not setting a timeout.

### Copy test results from Firebase to CircleCI

```sh
gsutil -m cp -r -U "`gsutil ls gs://test-lab-<some random ID>-<some other random ID> | tail -1`*" /root/workspace/firebase/
```

If your instrumented tests run fine, the test results and the test coverage report will be generated and stored on Firebase Storage. For free users, the files will be automatically deleted after 30 days, so you may want to copy them to CircleCI. You are given one Firebase Storage with a non-customizable ID. We must know what this ID is if we want to copy files from Firebase Storage to CircleCI.

Firebase does not provide free users an easy way to know this ID. A workaround is to manually run a test on Firebase Test Lab once and have it generate the test results. It doesn’t matter if it is an instrumented test or Robo test. And it doesn’t matter if the test succeeds or fails. When the test completes, go to Firebase Console, click to view the details of the test.

![Firebase](/assets/img/posts/2017/12/06/firebase.png "Firebase")

By clicking VIEW SOURCE FILES, it will open your Firebase Storage. The ID is in the URL:

`https://console.cloud.google.com/storage/browser/test-lab-<some random ID>-<some other random ID>/2017-12-04_04:16:23.701595_DgjM/Nexus5X-26-en_US-portrait?authuser=1`

Note your Firebase Storage ID and use it in the `gsutil` command above.

### Report test coverage

To generate an unified test coverage report from unit tests and instrumented tests, we need to copy the test coverage report generated by Firebase Test Lab to the correct path.

```sh
mkdir -p app/build/outputs/code-coverage/connected && cp firebase/Nexus5X-26-en_US-portrait/artifacts/coverage.ec app/build/outputs/code-coverage/connected/coverage.ec
```

With both coverage reports at the correct paths, we can run `jacocoTestReport` to generate a XML report for other coverage analysis services, as well as a HTML report for human consumption.

### Deploy

The final step is to deploy the release APK. This is similar to `build_debug` task so I will skip it.

## Conclusion

From my experience using CircleCI 2.0 with Firebase Test Lab, the build time of my sample ]open source project](https://github.com/ayltai/Newspaper) has been reduced from 30–45 minutes to 15–20 minutes, about 50% speed improvement and significant cost reduction.

There is one caveat though: in CircleCI 2.0, running tasks cannot be automatically canceled by a Git push to the same branch. If you are on a paid plan, and you do Git push very frequently, it may end up costing you more than CircleCI 1.0.
