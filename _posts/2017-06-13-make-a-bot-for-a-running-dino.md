---
layout: post
title: Make a bot for a running dino in Chrome
thumbnail-img: /assets/img/chrome_dino.png
tags: [chrome, visual studio]
comments: true
---
Not so long ago I desired to break the record in a mini-game in Chrome browser. If someone does not know, if you do not have access to the Internet and your browser is Chrome — press the space bar or, in case of mobile phones, tap on the screen and a mini-game appears to launch.

Let’s start creating it. The principle of the bot is that we parse the pixel color at a certain distance from the character in the loop and check if the color of the pixel is equal to the color of the cactus, then we jump. Else — just keep running (do nothing).

![](https://cdn-images-1.medium.com/max/2000/1*n4tsYc2A3ZJbRPqIwYthUA.png)

To begin with, lets find the right pixel. In my case, this pixel is on coordinates: “775x250”. I take the correct pixel by one above the highest hillock on the road and at a distance from the character derived by trial and error. Also should be noted that my screen resolution is 1920x1080 21.5" and if you have different, it will work crookedly.
So, lets create a console application in VS.

First, we declare the variables for the pixel coordinates.
```cs
public static int x = 775;         
public static int y = 250;
```
Then connect some DLL’s to the job.
```cs
[DllImport("user32.dll")]         
public static extern IntPtr GetDC(IntPtr hwnd);         

[DllImport("user32.dll")]         
public static extern int ReleaseDC(IntPtr hwnd, IntPtr hDC); 

[DllImport("gdi32.dll")]
public static extern uint GetPixel(IntPtr hDC, int x, int y);
```

Now lets add some code for the handler.
```cs
IntPtr hDC = GetDC(IntPtr.Zero);             
  while (true) {
    uint pixel = GetPixel(hDC, x, y);                 
    if (pixel == 5460819) {                     
      SendKeys.SendWait("{UP}");
    }
  }
```

The code is taken in an infinite loop.

```cs
if (pixel == 5460819)
```

This means that when the color of the pixel is equal to the color of the cactus in the Decimal encoding, the up arrow is pressed.

```cs
SendKeys.SendWait("{UP}");
```

Then we need to connect the two links.

```cs
using System.Diagnostics;
using System.Windows.Forms;
```

At the moment, the dino easily overcomes 500 game meters, and then it is trapped in the sight of birds and night. To solve these problems, I will write the following article. Good luck!

UPDATE: You don’t have to disconnect to play the game — just enter [chrome://dino/](chrome://dino/) in the address bar.

<style>.bmc-button img{height: 34px !important;width: 35px !important;margin-bottom: 1px !important;box-shadow: none !important;border: none !important;vertical-align: middle !important;}.bmc-button{padding: 7px 15px 7px 10px !important;line-height: 35px !important;height:51px !important;text-decoration: none !important;display:inline-flex !important;color:#ffffff !important;background-color:#5F7FFF !important;border-radius: 5px !important;border: 1px solid transparent !important;padding: 7px 15px 7px 10px !important;font-size: 22px !important;letter-spacing: 0.6px !important;box-shadow: 0px 1px 2px rgba(190, 190, 190, 0.5) !important;-webkit-box-shadow: 0px 1px 2px 2px rgba(190, 190, 190, 0.5) !important;margin: 0 auto !important;font-family:'Cookie', cursive !important;-webkit-box-sizing: border-box !important;box-sizing: border-box !important;}.bmc-button:hover, .bmc-button:active, .bmc-button:focus {-webkit-box-shadow: 0px 1px 2px 2px rgba(190, 190, 190, 0.5) !important;text-decoration: none !important;box-shadow: 0px 1px 2px 2px rgba(190, 190, 190, 0.5) !important;opacity: 0.85 !important;color:#ffffff !important;}</style><link href="https://fonts.googleapis.com/css?family=Cookie" rel="stylesheet"><a class="bmc-button" target="_blank" href="https://www.buymeacoffee.com/kip0d"><img src="https://cdn.buymeacoffee.com/buttons/bmc-new-btn-logo.svg" alt="Buy me a coffee"><span style="margin-left:5px;font-size:28px !important;">Buy me a coffee</span></a>

