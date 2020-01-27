---
layout: page
title: Setup
permalink: /setup/
---

There are a few steps to setup for the workshop. We strongly recommend that you complete these before the worksop starts. If you run into problems, please contact the workshop leader at the e-mail address on the home page. We will also provide installation and setup help at the workshop if required, but you will get more out of the training if you can complete them ahead of time.

There are three steps to the setup:

   1. Ensure your laptop has the bash shell installed
   2. Ensure that your bash shell is equipped with an SSH client
   3. Setup an account on the HPC system, Cirrus, that we will be using for the workshop
   
These steps are described in more detail below.

## bash Shell

To participate in an HPC Carpentry workshop, you will need access to a bash shell. Please make sure to install everything (or at least to download the installers) before the start of your workshop. In addition, you will need an up-to-date web browser.

We maintain a list of common issues that occur during installation as a reference for instructors that may be useful on the [Configuration Problems and Solutions wiki page](https://github.com/swcarpentry/workshop-template/wiki/Configuration-Problems-and-Solutions).

### Windows


1. Download the [Git for Windows installer](https://git-for-windows.github.io/)
2. Run the installer. The exact questions you are asked will vary by version of git bash, but in general, keep all defaults with the following exceptions:
   - Most importantly, choose 'Use Windows as default console' rather than 'Use MinTTY'
   - If given the option of 'Choosing the default editor used by Git', choose 'Use the Nano editor by default'
   - Make sure 'Use Git from the Windows Command Prompt' is selected. Depending on version, this may be called 'Git from the command line and also from 3rd-party software.'  
   
This will provide you with bash (and SSH) in the Git Bash program.
   
The video below talks through installing and opening git bash v2.7.2. You will not need to additionally download the Software Carpentry Windows Installer that is mentioned in the video for this workshop.

<iframe width="560" height="315" src="https://www.youtube.com/embed/339AEqk9c-8" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>



### MacOS

The default shell in all versions of Mac OS X is Bash, so no need to install anything. You access Bash from the Terminal (found in /Applications/Utilities). You may want to keep Terminal in your dock for this workshop.

### Linux

The default shell is usually Bash, but if your machine is set up differently you can run it by opening a terminal and typing bash. There is no need to install anything.

## SSH

All students should have an SSH client installed.
SSH is a tool that allows us to connect to and use a remote computer as our own.
Please follow the directions below to install an SSH client for your system.

**Windows**

You should have SSH available in Git Bash after you installed it according to the instructions above.

An alternative is to install MobaXterm from [http://mobaxterm.mobatek.net](http://mobaxterm.mobatek.net). You will want to get the Home edition (Installer edition). However, if Git Bash works, you do not need this.

**macOS**

macOS comes with SSH pre-installed, so you should not need to install anything.

**Linux**

Linux users do not need to install anything, you should be set!

## Account on Cirrus

We would like to invite you to sign up for your account on Cirrus, the HPC machine that will be available to you during the workshop, and also for a short while afterwards, so you can complete practical exercises and put what you have learned into practice.

To sign up for a machine account, you must first register for an [account on SAFE](https://tier2-safe.readthedocs.io/en/latest/safe-guide-users.html)

Once you have a SAFE account, you can request your machine account under project tc008: 
[https://tier2-safe.readthedocs.io/en/latest/safe-guide-users.html#tier-2-facilities-accounts-passwords](https://tier2-safe.readthedocs.io/en/latest/safe-guide-users.html#tier-2-facilities-accounts-passwords)

