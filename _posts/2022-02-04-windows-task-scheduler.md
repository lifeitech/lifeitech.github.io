---
title: Automate Your Routines with Task Scheduler
date: 2022-02-04 10:33:00 +0800
categories: [Programming]
tags: [windows, cmd]
image:
  src: /assets/imgs/windows-task-scheduler/head.jpg
  alt: "windows task scheduler"
  width: 800
  height: 500
---

> If you are using the windows operating system, do you know you can use the Task Scheduler application to run tasks at specific time? In this post I will show you how to do this. We'll also look at how to mute and unmute computer, play music, and open websites, all through the command line.

## Set up a task in Task Scheduler

Type "task scheduler" in search bar. You should see the app appearing. Open the app.

![windows task scheduler](/assets/imgs/windows-task-scheduler/1.png){:w=300, h=300 style="max-width: 80%"}

### Step 1. create a task

Click "Create Basic Task" on the right hand side.

![windows task scheduler](/assets/imgs/windows-task-scheduler/1.start.jpg){:w=300, h=300 style="max-width: 80%"}

Give your task a name and a description. Click "Next".

![windows task scheduler](/assets/imgs/windows-task-scheduler/2.png){:w=300, h=300 style="max-width: 80%"}

### Step 2. set up a trigger condition

Set up the time you want your script to be executed. You can run a task daily, weekly, monthly, or when the computer starts. Here we select "Daily". Click "Next", and set up your time

![windows task scheduler](/assets/imgs/windows-task-scheduler/3.png){:w=300, h=300 style="max-width: 80%"}
![windows task scheduler](/assets/imgs/windows-task-scheduler/4.png){:w=300, h=300 style="max-width: 80%"}

### Step 3. specify your program path

Click "Start a program" and "Next". Input the location for your script, and click "Next".

![windows task scheduler](/assets/imgs/windows-task-scheduler/5.png){:w=300, h=300 style="max-width: 80%"}
![windows task scheduler](/assets/imgs/windows-task-scheduler/6.png){:w=300, h=300 style="max-width: 80%"}

Click "Finish", and you are done.

Instead of "Create Basic Task", with "<mark>Create Task</mark>" you can execute more than one script with more than one trigger time. You also have more options about when to run a task.

![windows task scheduler](/assets/imgs/windows-task-scheduler/triggers.png){:w=300, h=300 style="max-width: 80%"}
![windows task scheduler](/assets/imgs/windows-task-scheduler/actions.png){:w=300, h=300 style="max-width: 80%"}

That's it! Having learned the steps for using the application, let's now look at some programs that we can execute in Task Scheduler. 

## Example 1: play a piece of music everyday

Here is a real example. Suppose you are asked to play some music at 10:00AM everyday in your office, to remind your colleagues for a break. Specifically, your computer is muted, and you need to perform three steps:

1. unmute your computer;
2. play the music;
3. mute your computer.

How would you do that? Let's go through a script step by step.

### Step 1. unmute your computer

As for now, there is no "clean" way to adjust volume in windows through command line. A workaround solution is

```shell
(echo Set WshShell = Wscript.CreateObject^("Wscript.Shell"^) 
echo WshShell.Sendkeys "¡­")>VolumeSwitch.VBS 
VolumeSwitch.VBS&del /f /q VolumeSwitch.VBS
```

which is to write pressing the virtual Volume Mute key (value `<0xAD>`) command to a `.vbs` file, execute the file and then delete it.

### Step 2. play the music

```shell
start music.mp3
timeout /t 200 /nobreak
taskkill /im wmplayer.exe /t /f
```

- Simply call `start` with the filename to play the music with the default player (suppose it is windows media player). 
- After you finished playing the music, you want to end the process, so you call `taskkill`. 
  - The argument `/im <imagename>` specifies the image name of the process to be terminated, in this case it is `wmplayer.exe`. 
  - `/t` ends the process and any child processes started by it. 
  - `/f` specifies that processes be forcefully ended.
- You need to wait for the music to finish playing before killing the process. Use `timeout` to pause the execution of the script for the length of the music. Otherwise, the process will be immediately killed and so the music would not be played.
  - The argument `/t <seconds>` specifies the time to wait before next command is executed. In this case it is 200 seconds.
  - `/nobreak` means ignore user key strokes.

### Step 3. mute your computer

Run the same code in step 1 again to switch off your volume.

```shell
(echo Set WshShell = Wscript.CreateObject^("Wscript.Shell"^) 
echo WshShell.Sendkeys "¡­")>VolumeSwitch.VBS 
VolumeSwitch.VBS&del /f /q VolumeSwitch.VBS
```

The complete script is shown below. Name your file with extention `.bat` (for "batch" file).

```shell
@echo off 
(echo Set WshShell = Wscript.CreateObject^("Wscript.Shell"^) 
echo WshShell.Sendkeys "¡­")>VolumeSwitch.VBS 
VolumeSwitch.VBS&del /f /q VolumeSwitch.VBS

start music.mp3
timeout /t 200 /nobreak
taskkill /im wmplayer.exe /t /f

(echo Set WshShell = Wscript.CreateObject^("Wscript.Shell"^) 
echo WshShell.Sendkeys "¡­")>VolumeSwitch.VBS 
VolumeSwitch.VBS&del /f /q VolumeSwitch.VBS

exit
```

## Example 2: open several websites

If you are learning a new language, you might have the routine of opening several learning websites at once, for example a dictionary site, a Google Translate page, and a tutorial. Or you might open several news websites every morning in order to check the latest news. You can have a simple windows script for doing that, without opening every website manually every time.

```shell
start www.wsj.com
start www.reuters.com
start www.twitter.com
``` 

## Example 3: write script in Python

If you want to execute more complicated functionalities, or if you are not comfortable with windows commands but rather prefer to write your tasks in Python, you can use the `pyinstaller` package to convert your Python script to an executable `.exe` file. Then you are able to run it either through double click, or the Task Scheduler. 

To install the package, run

```shell
pip install pyinstaller
```

Then simply call

```shell
pyinstaller --onefile yourscript.py
```

in the command line to convert your Python script to a  single `.exe` executable file.

## Summary

In this post, I showed how to use the Task Scheduler application in windows. With Task Scheduler, it is possible to execute scripts and programs when specific conditions are met. This can automate your routines and save your time and energy, setting you free from repetitive tasks. I discussed some examples, like playing an audio file and open several websites at once. If you find yourself repeating the same tasks very often on your PC, it may be a good idea to write them down in a script, and execute the script as needed.

<hr>
Cite as:

```bibtex
@article{lifei2022scheduler,
  title   = "{{ page.title }}",
  author  = "Li, Fei",
  journal = "{{ site.url }}",
  year    = "2022",
  url     = "{{ page.url | absolute_url }}"
}
```