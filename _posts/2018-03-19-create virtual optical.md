---
layout: post
category: ""
title:  "Save to virtual optical"
tags: [AS400]
---

Copy from https://www.ibm.com/support/knowledgecenter/en/ssw_i5_54/rzaiu/rzaiuvirtualoptical.htm


Perform the following steps to save data to virtual optical media.

Ensure that the system has enough disk space to hold all the virtual images you are going to create for your save operation.
Create a virtual optical device.
CRTDEVOPT DEVD(virtual-device-name)RSRCNAME(*VRT) ONLINE(*YES)+ 
TEXT(text-description)
Vary on the virtual optical device.
VRYCFG CFGOBJ(virtual-device-name) CFGTYPE(*DEV) STATUS(*ON)
Create an image catalog for your save operation.
CRTIMGCLG IMGCLG(catalog-name) DIR(catalog-path) CRTDIR(*YES) +
TEXT(image-description)
Add a new image catalog entry with a size of 48MB to 16GB. If you are performing a SAVSYS, the first volume must be at least 1489 MB to accommodate the Licensed Internal Code. If you plan to save the full operating system, add a new image catalog entry with a size of 4GB. If you plan to duplicate image catalogs to physical media, then ensure you select a virtual image size that matches the size of the media you plan to write to.
ADDIMGCLGE IMGCLG(catalog-name) FROMFILE(*NEW) TOFILE(file-name) +
IMGSIZ(*DVD4700) TEXT(text-description)   
ADDIMGCLGE IMGCLG(catalog-name) FROMFILE(*NEW) TOFILE(file-name) +
 IMGSIZ(*CD650) TEXT(catalog-descritpion)
Repeat this step for the number of desired images. You should add the images in the same order as you plan to restore from them. The virtual images provide spanning capability, with sequence numbers continuing from one volume to the next.
Load the image catalog. This step associates the virtual optical device to the image catalog. Only one image catalog at a time can be associated with a specific virtual optical device.
LODIMGCLG IMGCLG(catalog-name) DEV(virtual-device-name) OPTION(*LOAD)
Initialize the new volume.
INZOPT NEWVOL(volume-name) DEV(virtual-device-name) TEXT('volume text')
Repeat this step for the number of new images you want to initialize. Use the WRKIMGCLGE (Work with image catalog entries) command to select the image to be initialized or use the LODIMGCLGE (Load or unload image catalog entry) command to continue to the next volume to be initialized.
LODIMGCLGE IMGCLG(catelog-name) IMGCLGIDX(2) OPTION(*MOUNT)
LODIMGCLGE IMGCLG(catelog-name) IMGCLGIDX(1) OPTION(*MOUNT)
When you have completed initializing the new volumes, leave the first entry in mounted status.
Run the save command for your desired save operation, listing the virtual optical device in the DEV parameter.
