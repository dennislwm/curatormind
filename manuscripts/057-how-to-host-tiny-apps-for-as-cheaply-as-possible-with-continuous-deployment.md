# How to host tiny apps for as cheaply as possible with continuous deployment

## Introduction

[GitHub](https://github.com) isn't just for version control of source code. It's also for continuous integration and deployment of your workflow. Using a GitHub Actions gives you the ability to build run applications, test applications, build docker images, run docker containers, and deploy containerized applications to your host. And if you're a serious about Devops, you can create your own GitHub Action to manage your workflow.

In this tutorial you'll use a GitHub Action to manage your continuous integration and deployment on a repository. You'll create a workflow file, a custom action file, a python application that can accept parameters and generate a test coverage report that is sent to your email address.

## Prerequisites

* [GitHub](https://github.com) account.
* I used [Visual Studio Code](https://code.visualstudio.com/). but you can use any code editor.

## Step 1 - Create a new workflow

In this section, ensure that you are in the folder **.github/workflows/** under your root project folder of your repository.

Let's create a new workflow file in Visual Studio Code ["vscode"] editor, and name the file **main.yml**.

This workflow runs on any push or pull request, using an Ubuntu Linux base image, and Python version 3.6. If the series of steps are successful, a test coverage is sent to **coveralls.io**.

Type the following code into the above YML file:

     #+-----------------------------------------------------------------------------------------+
     #|                                M A I N   W O R K F L O W                                |
     #+-----------------------------------------------------------------------------------------+
     name: main
     #+-----------------------------------------------------------------------------------------+
     #|                            E X T E R N A L   T R I G G E R S                            |
     #+-----------------------------------------------------------------------------------------+
     on: ["push", "pull_request"]
     jobs:
       #+-----------------------------------------------------------------------------------------+
       #|                                 C U S T O M   J O B                                     |
       #+-----------------------------------------------------------------------------------------+
       udemyenrol:
         #----------------------------------------
         # Type of runner that the job will run on
         runs-on: ubuntu-latest
         steps:
           #---------------------------------------------------------
           # Runs third-party actions
           #   Checks out your repository under $GITHUB_WORKSPACE
           #   Setups Python and specify version
           - uses: actions/checkout@v2
           - name: List directories
             run: ls -laR
             working-directory: ./udemyenrol
           - uses: actions/setup-python@v2
             with:
               python-version: 3.7
           #------------------------------------------------
           # Runs your custom action
           - name: udemyenrol_app
             uses: ./udemyenrol
             with:
               gmail: ${{ secrets.GITHUB_GMAIL }}
               gmail_app_password: ${{ secrets.GITHUB_GMAIL_APP_PASSWORD }}
           #------------------------------------------------
           # Runs a set of commands using the runner's shell
           - name: Install Python Dependencies
             run: |
               pip install -U pip wheel coverage coveralls
               python --version
           - name: Test Coverage
             run: coveralls
             env:
               GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

Set the workflow name.

     name: main

Set the actions that trigger the workflow.

     on: ["push", "pull_request"]

Set the job name and runner.

     jobs:
       udemyenrol:
         runs-on: ubuntu-latest

First step, we prepare the build environment by checking out the code from your repository and list all files.

     steps:
       - uses: actions/checkout@v2
       - name: List directories
         run: ls -laR
         working-directory: ./udemyenrol

WARNING: We set the **working-directory** to your subfolder *udemyenrol*. This ensures that your Dockerfile ADD and COPY commands get its source files from the correct folder.

Second, we setup Python with a specified version.

       - uses: actions/setup-python@v2
         with:
           python-version: 3.7

Next step, we install dependencies.

       - name: Build
         run: |
           pip install -U pip wheel
           python --version

If tests are successful, send test coverage report to Coveralls with an authentication token, *secrets.GITHUB_TOKEN*.

      - name: Test Coverage
        run: coveralls
          env:
            GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

## Step 2 - Create a new action

In this section, ensure that you are in the folder **.github/actions/** under your root project folder.

Let's create a new action file in Visual Studio Code ["vscode"] editor, and name the file **udemyenrol_app.yml**.

We will start with using parameters that are required.

Type the following code into the above YML file:

     #+-----------------------------------------------------------------------------------------+
     #|                                C U S T O M   A C T I O N                                |
     #+-----------------------------------------------------------------------------------------+
     name: "Docker Image Build"
     description: "Build Docker image using Dockerfile"
     
     #+-----------------------------------------------------------------------------------------+
     #|                           E X T E R N A L   V A R I A B L E S                           |
     #+-----------------------------------------------------------------------------------------+
     inputs:
       gmail:
         required: true
         description: "Gmail address for both fields from and to"
       gmail_app_password:
         required: true
         description: "Gmail app password from [https://myaccount.google.com/apppasswords"](https://myaccount.google.com/apppasswords)
     
     runs:
       using: "docker"
       image: "Dockerfile"

Set the action name and description.

     name: "Docker Image Build"
     description: "Build Docker image using Dockerfile"

Set the parameters for this action.

     inputs:
       email:
         required: true
         description: "Gmail address for both fields from and to"

Set the steps for this action.

     runs:
       using: "docker"
       image: "Dockerfile"

## Conclusion

In this tutorial, you used a Telegram Bot to query your orders from a Metatrader 4 client. You can use this approach to manage your order flow, view account details, open and close orders, or even broadcast trade signals to a Telegram group or channel.

## Get the Source Code

You can download the above source code from this GitHub repository dennislwm/pydocker-cli.

## What To Do Next

You can further extend your Bot in several meaningful ways:

1. Implementing Authentication - This is to ensure that only approved users have access to the Bot commands.
1. Implementing Open and Close Orders - This is to allow opening and closing orders using the Bot.
1. Implementing Modify SL and TP - This is to allow modifying the StopLoss and TakeProfit of an order.
1. Implementing Add and Delete Pending Orders - This is to manage pending orders using the Bot.
1. Create a Chart Query Tool: This is to allow users to query chart values, such as prices and indicator values for an instrument.
1. Broadcast Trading Signals in a Channel - This is to allow users to subscribe to your trade signals (one way communication).
1. Copy Trading Signal to a MT4 Client - This is to allow users to trade your signals automatically (requires a Client Bot).