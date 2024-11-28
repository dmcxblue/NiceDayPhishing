# A Nice Day Phishing

## Scripting Interpreters

Scripting Interpreters are agreat method to achieve Command or Shellcode Execution, but one of many problems is that they:

- Are not Native to Windows
- Not always easy to use
- Limited to Command or Shellcode execution, some have both!!
- We requiere these to not be installed on the Users workstation

### Python

Python is a strong language to demonstrate the capabilities of these interpreters the following screenshot demonstrates how just printing out STRINGS in their language will not get flagged, it seems that this behavior is caused because AMSI has a better grasp on `Windows Native Files` see the example below:

![amsi](R&D%20Images/amsi.png)

To make python feasable to our needs we have to consider this:

- Size
- Modules
- Version

A compiled portable version of python will give us enough for execution such as the following:

![python]](R&D%20Images/python1.png)

We encounter the issue that some Modules are missing when trying to go beyond to executing Win32 apis now the challenge is to:
- Compile a portable version with the needed modules

The idea came from here:
https://stackoverflow.com/questions/44488174/how-to-make-python-portable

Now with some debugging and lots of banging the portable version of pytohn works correctly and the modules that I needed were in the binary

![python 2](R&D%20Images/python2.png)

With this fully functional we can utilize the portable version of python, a loader and call our payload, since the files and binary won't run our files via double-click we need to use a parent program that will call these, and a great alternative is LNK files

MORE TO COME!!

## ADS

Alternate Data Streams store additional information about a file, such as metadata or comments, without changing the file itself APT actors are known to use this Data Stream to deliver payloads, now we will use this to deliver our Initial Access attachments

![ADS](R&D%20Images/ads.png)

Now when delivering payloads with ADS we ecnounter some issues:

- Data is stripped if sent remotely
- ADS cannot be reached via dobule-click, or can it?!
- We need another parent file to call our ADS Stream

With these in consideration we can see how ADS works first in the image below will demonstrate how a TXT fiel sent over the internet is stripped away from its ADS data when sent from A -> B

![ADS 1](R&D%20Images/ads1.png)

So what can we do here if the DATA is stripped, the idea was to utilize containers usch as (ZIP, RAR, ISO) but unfortunately the ADS DATA inside the containers will also get stripped, the approach is to find containers that won't remove the ADS now we have 2 options: WINRAR and 7z:

![Winrar](R&D%20Images/winrar.png)

![7z](R&D%20Images/7z.png)

In the previos screenshot we demonstrate how WIM files do not strip the data if told by 7z and WINRAR has the option to keep the Data stream from files, with this we have a method to hide our payloads in ADS, now to get a full demo on this technique will use a script file (BAT) to grab the ADS data and reconstruct it to build our Initial Access Payload and when taking a look at the ADS, there is no Zone.Identifier on the payload

![ADS - MOTW](R&D%20Images/ads2.png)
