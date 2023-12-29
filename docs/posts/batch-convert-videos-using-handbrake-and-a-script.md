---
title: Batch convert videos using Handbrake and a script
date: 2012-05-19
authors:
    - jorge
categories:
    - Windows
---
So I mainly use Linux, but for some tasks I turn to Windows. In this particular case it was setting up my iPad so that my wife could take it to Spain with her, packed with movies, to entertain the kids. This included the use of iTunes. I’m sure there’s some way to make it work in Linux as well, but I thought I’d write a post based on a solution I found in Windows, for once.

I needed to convert existing **.avi**-files to the format the iPad uses. This is where the program [Handbrake](http://handbrake.fr/) comes into play. It’s excellent for converting from one format to another, but it’s the batch-convert part that isn’t too good. So how to solve this? Well, first install [Handbrake](http://handbrake.fr/), then proceed to create a **.bat**-script in the directory that contains the **.avi**-files. I do realize that [Handbrake](http://handbrake.fr/) has a **Add to queue** function, but that sort of takes away the point of batch-converting. Rather I specify a folder, then it convert every file in it, than me adding a file manually one by one.

Open up **Notepad**, and place the following bit of text in it:

```
FOR /F "tokens=*" %%G IN ('DIR /B /S *.avi') DO "C:\Program Files\Handbrake\HandBrakeCLI" -i "%%G" -o "%%G".mp4 --preset="iPad"
```

When saving the file, remember to select **All files** on the **Save as type**-field. Then type in **script.bat** in the **File Name** field.

So when you’re done with this, it’s just a case of double-clicking the script to batch convert all of the **.avi**-files in the directory the script is placed in.

Oh, another word of advice. [Make sure your Windows-machine doesn’t hibernate or go to sleep](http://www.avinashtech.com/microsoft/disable-auto-sleep-mode-windows/). I learned that the hard way.