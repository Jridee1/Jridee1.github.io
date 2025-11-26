---
title: Jacob Rider - Cybersecurity Portfolio
---

# SOAR Project

For this project, I wanted to create a playbook within Tines, and AI orchestration platform, that will send a Slack message and an email with details about a detection from LimaCharlie. Next, I will prompt the user if they want to isolate the machine based off the details received. Finally, I would isolate the machine using LimeCharlie if the user agrees, or send a message indicating no isolation if they decline. I did my best to draw that up here:

![Branching](SOARDrawup.png)

## Tools Setup

Using Vultr, a VM cloud provider, I configured a Windows VM. As I waited for that to build, I would need to create an installation key and the sensor key in order to install LimaCharlie on the Windows VM. After the VM is built, I'll head over to it and use the following Powershell command in the downloads directory to install LimaCharlie (Replace executable file and installation key with their respective components):

```
  (executable file) -i (installation key)
```
![Branching](LimaCharlieSuccess.png)
![Branching](LimaCharlieSuccess1.png)

In order for LimaCharlie to detect a hacktool, we need to download Lazagne from the Lazagne Project on their GitHub. Lazagne is an open source application that retrieves passwords stored on a local computer. We verify that its downloaded with Powershell and LimaCharlie:
```
  .\LaZagne.exe
```
![Branching](Lazagne.png)
![Branching](SOAR-Project.png)

The Arrows in the screenshot above indicate some important information we can use to create the detection and response rules within LimaCharlie.

## Detect Rule
The Detect rule's logic is as follows:
* The event type must be either New_Process or Existing_Process AND must be windows
* file_path ends with lazagne.exe
* OR command_line ends with all
* command_line contains lazagne
* OR hash == lazagne hash

That comes out to look like this:

![Branching](Detectrule.png)

## Response Rule
Within the response rule you'll see the action "report" this just means that if a certain event meets the criteria for the detection, an event will be generated in the detection tab in LimaCharlie:

![Branching](Responserule.png)

Now lets see if the rules we created works. To do this, we'll use the command 
```
  .\LaZagne.exe all
```
And we see detections with the rule we created in LimaCharlie!:

![Branching](DetectionsSuccess.png)

## Slack and Tines setup

Slack is very straightforward to setup, just creating a free account and creating a new channel called "alerts". Tines is where a majority of the setup work is going. First we will need to link our Tines story to LimaCharlie. This is done in LimaCharlie by going to Outputs > Add Output > Detections > Tines. When it asks for our Destination Host, we will link the url from the webhook in the picture below.

![Branching](TinesStory2.png)

Now lets use that same command we used above to generate another detection to see if we get it in our Tines story:
```
  .\LaZagne.exe all
```

![Branching](TinesStory1.png)

We have now successfully linked LimaCharlie to our Tines story!

## Tines Final Look

Below is the complete story on Tines. I will break it down section by section:

![Branching](Final.png)

### Send Email
