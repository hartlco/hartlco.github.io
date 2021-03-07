---
layout: post
date: "2021-03-07 13:00"
title: "Release private Pods using GitHub Actions"
---
Another example of: "let's make a blogpost out of this cobbled together note, so I don't have to figure it out in a year again".

Using CocoaPods' [private Pods](https://guides.cocoapods.org/making/private-cocoapods.html) allows you to modularize your Application while hosting the code in different private repositories[^1]. Releasing a new version of your private Pod can be time-consuming and is therefore a perfect opportunity for automation, in this case using GitHub Actions.
This workflow assumes that you already created your private spec repo and successfully pushed an initial version of your Pod.

I substitued all variables with `<>`. Example of the file with actual values: [Gist to release.yml](https://gist.github.com/hartlco/34e6e3cc35221eb4bc5c30f86cbd20d1)

## When to trigger the workflow
You first need to decide on when to [trigger](https://docs.github.com/en/actions/reference/events-that-trigger-workflows) your automation workflow. In this example I decided to use the [workflow dispatch](https://github.blog/changelog/2020-07-06-github-actions-manual-triggers-with-workflow_dispatch/) trigger. This gives the engineer the freedom on when to trigger a new release while, providing additional information like the semantic version to be used or a name for the release[^2]. We start our `release.yml` with the following:

```yml
name: Release

on:
  workflow_dispatch:
    inputs:
      version:
        description: 'Version  (i.e. 1.1.3)'
        required: true
      name:
        description: 'Name of the Version'
        required: true
```

## Setup the job
We now need to decide and what machine to run our automation and install the required tools:

```yml
jobs:
  build:

    runs-on: macos-latest

    steps:
    - uses: actions/checkout@v2
      with:
        fetch-depth: 1
    - name: Bundle Install
      run: bundle install

```

We run the job on hosted Macs provided by GitHub actions `macos-latest`, as we require Xcode to release a new version of our module. We assume a `Gemfile` exists in the root of the repository that defines the dependency on `gem cocoapods`. (To automatically bump the `.podspec`-version, we also require `gem "fastlane"`). We install the tools using `Bundle install`.

## Add the private spec repo to CocoaPods
We need to make the CocoaPods installation of the Action Runner aware that our private spec repo exists and give it the rights to modify this repo:

```yml
    - name: Add Pod Spec
      run: pod repo add <NAME_OF_REPO> https://<GIT_ACTOR>:<GIT_TOKEN>@github.com/<ORG>/<REPO_NAME>.git
```

We are passing the username and personal access token of a user with write permissions to the spec repo along the `pod repo add` command. I suggest defining the username and the access token as [encrypted secrets](https://docs.github.com/en/actions/reference/encrypted-secrets).

## Optional: Bump the Podspec version
We can use the `version` parameter passed along the workflow_dispatch trigger and a [fastlane action](https://docs.fastlane.tools/actions/version_bump_podspec/) to automatically update the `podspec` version of the dependency. We commit these changes and automatically open a PR with it.

```yml
    - name: Bump Podspec version
      run: fastlane run version_bump_podspec path:"<NAME_OF_POD>.podspec" version_number:<VERSION>
    - name: Create Pull Request
      uses: peter-evans/create-pull-request@v3
      with:
        title: Release/<VERSION>
        base: main
        branch: Release/<VERSION>
        body: |
          Release: <VERSION>
          Changes:
          - Bump version to <VERSION>
```


## Create a release tag and push the new version
As a last step, we create a release on GitHub[^3] and push the new version of our pod to the spec repo:

```yml
    - name: Create Release
      uses: actions/create-release@v1
      env:
        GITHUB_TOKEN: <GIT_TOKEN>
      with:
        commitish: Release/<VERSION>
        tag_name: "<VERSION>"
        release_name: "<VERSION>"
        body: "<VERSION>"
        commit-message: "Bump pod version"
    - name: Push Pod Spec
      run: pod repo push <NAME_OF_REPO> <NAME_OF_POD>.podspec
```

## Run the action
You can now run the action using GitHub's UI, as outlined again [here](https://github.blog/changelog/2020-07-06-github-actions-manual-triggers-with-workflow_dispatch/).

Bye!

[^1]: The necessity of hosting your code in different repositories and the upsides/downside this brings is a whole different topic. Not even talking about the choice of dependency manager here.
[^2]: The name could be used to automatically create a new release on a CHANGELOG file or give the created GitHub Release more context.
[^3]: This automatically creates a git tag. We use the version number and name provided by the trigger to populate the title/body of the release.