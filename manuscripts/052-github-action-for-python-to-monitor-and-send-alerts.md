# GitHub Action for Python to Monitor and Send Alerts

## Introduction

**TL;DR**



## Prerequisites



## Step 1 - Configure GitHub Actions

**New Repository**

Open a web browser, and navigate to your GitHub account. Click on "**New Repository**" and type any name of your repository, e.g. **pyaction**. Click "**Create Repository**" button to create your new repository. For example, **USER/pyaction** ["remote repository"], replacing USER your GitHub username.

The first method to create a new GitHub Action is via the **Actions** tab, This method creates a default YML script **python-app.yml** in the subfolder *.github/workflows*.

You can read the article [Automate Python Testing with GitHub Actions](https://medium.com/swlh/automate-python-testing-with-github-actions-7926b5d8a865) to learn more.

     # This workflow will install Python dependencies, run tests and lint with a single version of Python
     # For more information see: [https://help.github.com/actions/language-and-framework-guides/using-python-with-github-actions](https://help.github.com/actions/language-and-framework-guides/using-python-with-github-actions)
     name: Python application
     on:
       push:
         branches: [ master ]
       pull_request:
         branches: [ master ]
     jobs:
       build:
         runs-on: ubuntu-latest
         steps:
         - uses: actions/checkout@v2
         - name: Set up Python 3.8
           uses: actions/setup-python@v2
           with:
             python-version: 3.8
         - name: Install dependencies
           run: |
             python -m pip install --upgrade pip
             pip install flake8 pytest
             if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
         - name: Lint with flake8
           run: |
             # stop the build if there are Python syntax errors or undefined names
             flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
             # exit-zero treats all errors as warnings. The GitHub editor is 127 chars wide
             flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics
         - name: Test with pytest
           run: |
             pytest

However, we will use the second method, which is to create our custom script in the subfolder *.github/workflows*.

First, clone the remote repository to into a new folder, e.g. **pyaction** ["local repository"].

     $ git clone [ssh://git@github.com/USER/pyaction.git](ssh://git@github.com/dennislwm/pyaction.git) pyaction

Next, create a subfolder **.github/workflows** in the root folder of your local repository. Within this subfolder, create a new file with any name, e.g. **pymonitor.yml**.

     $ cd pyaction
     $ mkdir .github
     $ mkdir .github/workflows
     $ touch .github/workflows/pymonitor.yml

## Step 2 - Configure Workflow Events

You can configure your workflow to use more than one webhook event to trigger a workflow run. The default YML script has two events that trigger a workflow run, i.e. push and pull_request.

Example of using a list of events:

     on: [push, pull_request]

You can read GitHub documentation on [Events that trigger workfflows](https://docs.github.com/en/actions/reference/events-that-trigger-workflows) to learn more about events.

We add two events, **worker_dispatch** and **schedule** to our list of events, and remove **pull_request**. We list the events separately as we need to configure each individual events.

     on:
       push:
       worker_dispatch:
       schedule:
         - cron:  '59 0 * * *'

**Push **event is self-explanatory, the workflow runs each time you perform a git push.

**Worker_dispatch** event allows you to manually trigger a workflow using either the GitHub API or from GitHub site.

**Schedule** event allows you to trigger a workflow at a schedule time using cron syntax. The above cron schedule reads as "trigger event at 00:59 every day".

Copy and paste the following code into the YML script:

     # This is a basic workflow to help you get started with Actions
     
     name: pymonitor
     
     # Controls when the action will run. Triggers the workflow on
     # events but only for the master branch
     # * * * * *  command to execute
     # ? ? ? ? ?
     # ? ? ? ? ?????? day of week (0 - 7) (0 to 6 are Sunday to Saturday)
     # ? ? ? ??????????? month (1 - 12)
     # ? ? ???????????????? day of month (1 - 31)
     # ? ????????????????????? hour (0 - 23)
     # ?????????????????????????? min (0 - 59)
     on:
       push:
       workflow_dispatch:
       schedule:
         - cron:  '59 0 * * *'

## Step 3 - Configure Virtual Environment and First Steps

Every job requires a virtual environment to run an operating system. The default YML script has the latest Ubuntu, i.e. ubuntu_latest.

Example of using an operating system.

     runs-on: ubuntu-latest

You can read GitHub documentation on [Virtual environments for GitHub-hosted runners](https://docs.github.com/en/actions/reference/virtual-environments-for-github-hosted-runners) to learn more about supported operating systems.

The checkout action is the first step that you must include in your workflow before other actions when:

* your workflow requires a copy of your repository's code
* you are referencing at least one action in the same repository

Example of using the standard checkout action.

     - uses: actions/checkout@v2

The next steps are similar to the default python YML script. We need to setup python and install dependencies.

Example of setting up python.

     - name: Set up Python 3.8
       uses: actions/setup-python@v2
       with:
         python-version: 3.8

You can browse and use actions built by GitHub in the organization [github.com/actions](https://github.com/actions).

Example of installing dependencies using a multi-line script.

     - name: Install dependencies
       run: |
         python -m pip install --upgrade pip
         pip install flake8
         if [ -f requirements.txt ]; then pip install -r requirements.txt; fi

The **requirements.txt** file is found in your repository, which lists your python dependencies.

Copy and append the following code into the YML script:

     jobs:
       build:
         runs-on: ubuntu-latest
         steps:
         - uses: actions/checkout@v2
         - name: Set up Python 3.8
           uses: actions/setup-python@v2
           with:
             python-version: 3.8
         - name: Install dependencies
           run: |
             python -m pip install --upgrade pip
             pip install flake8
             if [ -f requirements.txt ]; then pip install -r requirements.txt; fi



## Step 4 - Configure Final Steps

The next step is to run your Python script.

Example of running Python script.

     - name: Download data and save
       run: |
         python3 pymonitor.py

The final step is to update your repository.

Example of updating your repository.

     - name: Commit and push if changed
       run: |
         git add .
         git diff
         git config --global user.email "github-action-bot@example.com"
         git config --global user.name "GitHub Action Bot"
         git commit -m "Updated _ChartCls_0.1_Dbs_.png" -a || echo "No changes to commit"
         git push

## Step x - Setup Badge in README.md

The workflow generates a badge (image file) that you can use to show the status of the last build. Typically, this is embedded into your repository **README.md**.

For example, both the repository and workflow are named **pyaction** and **pymonitor**, so we insert the following line in your README.md file to embed the badge image.

     ![Build Status](https://github.com/USER/REPOSITORY/workflows/WORKFLOW/badge.svg)

## Conclusion

In this article, you have succesfully achieved:

* 

## Get the Source Code

You can download the above source code from GitHub repository dennislwm.

## What To Do Next

You can further explore the package in several meaningful ways:

* 

## Python, R and Metatrader for Happi Traders

[https://fxgit.work](https://yourls.fxgit.work/a001)

## FREE $100 CREDIT

[https://digitalocean.com](https://yourls.fxgit.work/001affdo)

## BUY A COFFEE

[https://ko-fi.com/dennislwm](https://yourls.fxgit.work/a002)

## BE A HAPPI PATREON

[https://patreon.com/dennislwm](https://yourls.fxgit.work/a003)

## REACH OUT

Dev.to: [https://dev.to/dennislwm](https://yourls.fxgit.work/a004)

GitHub: [https://github.com/dennislwm](https://yourls.fxgit.work/a005)

Twitter: [https://twitter.com/leetradetitan](https://yourls.fxgit.work/a006)

YouTube: [https://youtube.com/leetradetitan](https://yourls.fxgit.work/a007)