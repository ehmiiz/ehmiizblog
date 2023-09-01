--- 
title: "Using the PowerShell Module Z to Save Time in the Terminal" 
date: 2023-03-17T08:23:11+01:00 
draft: false
comments: true

---

{{< figure
  src="/pics/z_mod.png"
>}}


The module ["Z"](https://github.com/badmotorfinger/z) is a handy tool that can save you lots of time when navigating around your terminal. In this blog-post, we'll cover how to install it to user scope, how to configure it by jumping around the terminal to different places, and how it saves lots of time in the long run.

## Installing Z to your CurrentUser scope

To install Z, open a PowerShell terminal and run the following command:

```powershell
Install-Module z -Scope CurrentUser
```

This will install Z for your current user only. If you want to install it for all users on your system, you can use -Scope AllUsers instead.

## Configuring Z

Once you have Z installed, you can start configuring it by jumping around your terminal to different places. To do this, simply navigate to a directory that you frequently visit and then run z followed by a space and then part of the directory name. For example:

```powershell
cd C:\Users\MyUser\Documents\Projects\MyProject
cd \
z MyProject
```

This will add C:\Users\MyUser\Documents\Projects\MyProject to Z's list of directories. The more you use z with different directories, the more accurate it will become at predicting where you want to go.

## Saving Time with Z

Once you have Z configured with your frequently visited directories, you can start saving time by using z instead of typing out long paths.

It may not seem that much of a timer-saver at first glance, however the more you get used to using z, instead of cd/tab-completion for navigation, the more time you will save. And I would imagine if you use your terminal daily, the time-savings are huge.

## Z's a bash fork

That's right. The module is actually forked and ported from a bash script, what makes it super useful together with PowerShell is, you guessed it: [PSProviders](https://learn.microsoft.com/powershell/module/microsoft.powershell.core/about/about_providers?view=powershell-7.3).

Not only can you navigate around your file-system, you can also visit your local reg hive, cert store, and other PSProviders, to save them to your directory history.

```powershell
cd Cert:\LocalMachine\My\

z 'Cert:\LocalMachine\My'

z C:\

z my
```

- Here, you start out visiting your local certificate store.
- You save it to your z directory
- You navigate back to C root
- And back to your local cert store using only "z my"

This works with lots of different PSProviders, so try it out yourself!

## To sum it up

By installing, using and configuring Z, you can save lots of time when navigating around your terminal. By installing it to user scope and configuring it by jumping around your terminal to different places, you'll be able to quickly navigate to frequently visited directories and other PowerShell provider paths, with just a few keystrokes. Keep in mind that the more you use it, the better it gets, both from a functional standpoint but also the amount of time it saves you (So that you can drink coffee instead 😁 ).

### Happy Coding