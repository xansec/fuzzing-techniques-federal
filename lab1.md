# Lab 1a: Fuzzing a binary in Docker

## Overview

This guide will show you how to package our target binary, `ffmpeg`, and fuzz it with Mayhem.

**Time to complete**: About 5 minutes

## Before we begin!

If you haven't already, create a new account by navigating to
[app.mayhem.security](https://app.mayhem.security) and either choose:
   * Sign up: Create a local account on the Mayhem instance.
   * SSO Sign-In: Use an identity broker such as Google or Github to create your Mayhem account

![Mayhem Account Creation](assets/images/account-creation.png)

## Step 1. Create a new Run

Let's fuzz `ffmpeg` using an existing Docker image.

   * Create a new project by clicking "New Project" at the top of the screen.
![Create new project](assets/images/create-new-project.png)
   * We're analyzing a binary, so we'll go with "code". We'll use an existing image: `jrottenberg/ffmpeg`
![Use dockerhub image](assets/images/project-from-dockerhub.png)

## Step 2. Configure Run

In order to get this fuzzing, we need to make sure we're using the correct options for `ffmpeg`.
At it's core, this binary takes:
1. An input file, and
2. an output file
as well as some options telling `ffmpeg` what to do to the file. We really only need to modify the command section of this run.

   1. Under **Scan Duration**, set the analysis to run for 1 minute.
   2. Under **Commands**, set the command to `ffmpeg -i @@ -f null ignore.wav`. This tells Mayhem to generate a fuzzed file and use it as an input for `ffmpeg`. We will ignore the output, as we are only interested in fuzzing the target.

   ![Basic configuration](assets/images/basic-configuration-options.png)




## Step 3. Unleash Mayhem!

Click the "Create Project and Run" button at the bottom of your screen to begin analysis!

The run may take a few minutes to complete, as analysis and post-analysis
processing need to finish. Once the run completes, the web page should look
similar to this:

![Completed Run](assets/images/completed-run.png)


# Lab 1b: Build your own ffmpeg

## Overview

This guide will show you how to build your own container, push it to Mayhem, and start fuzzing.

**Time to complete**: About 5 minutes

## Step 1. Log in to Mayhem with the CLI

The `mayhem` CLI authenticates to the Mayhem server using an API token. The API
token  is available two places:

   * The [download screen](https://app.mayhem.security/-/installation)
   * In your profile settings, located in the bottom-right under your username, then
     "Account Settings", then "API Tokens".

Once you get your API token, log in the CLI:

```
mayhem login https://app.mayhem.security/ <YOUR API KEY>
```

![API Token Locations](assets/images/api-token-locations.png)

## Step 2. Create a Dockerfile.

Under the ![ffmpeg directory](ffmpeg), you will find a Mayhemfile,
a binary (`ffmpeg`), and a Dockerfile with a .TODO suffix.

You will need to add the relevant commands to get this Dockerfile running a working copy of `ffmpeg`

## Step 3. Build and push your image.

Once your Dockerfile is complete, you'll want to build it:

```
docker build -t ghcr.io/<yourname>/ffmpeg

```

You'll then want to push the complete image to Github's container registry:

```
docker push ghcr.io/<yourname>/ffmpeg
```

You'll then need to make the image public. You can do this by going to your:

Profile ->
  Packages ->
    Select `ffmpeg` ->
      Scroll to the bottom and select "Visibility" ->
        Set to Public

More info can be found [here.](https://docs.github.com/en/packages/learn-github-packages/configuring-a-packages-access-control-and-visibility#about-setting-visibility-and-access-permissions-for-packages)

## Step 4. Start the run with the Mayhem CLI.

For this tutorial, no changes to the Mayhemfile are neccesary.You can
start your run with:

```
mayhem run .
```

You should see output that looks similar to this:
```
WARNING: testsuite is not a file or directory, skipping
Run started: ffmpeg/ffmpeg/2
Run URL: https://app.mayhem.security:443/abrewer/ffmpeg/ffmpeg/2
ffmpeg/ffmpeg/2
```

Note the warning regarding the testsuite directory is normal for this tutorial.

Navigate to the Web interface for this run by clicking on the "Run URL" from
the output.

The run may take a few minutes to complete, as analysis and
post-analysis processing need to finish. Once the run completes, the web page
should look similar to this:

![Completed Run](assets/images/completed-run.png)

Congratulations!

You've just:
  * Learned how to build and push a Docker images
  * Learned how to start a Mayhem run from the CLI
