# Building Reality from Scratch

Here's a guide on how to build the Reality from Scratch HMD and controllers with 6DoF tracking. This guide is heavily based off of Relativty and HadesVR's guides, with an emphasis on being beginner-friendly.

<img src="images/uw-reality-labs/UW Reality Labs Icon.png" width="150" height="150" alt="UW Reality Labs Icon">

## Building the Basic HMD

### Materials

| **Component Type** | **Recommended Part** | **Count** |
| --- | --- | --- |
| IMU | MPU-9250 | 1 |
| MCU | Arduino Pro Micro | 1 |
| Display | 1440x1440 90Hz 2.9" LCDs | 2 |
| Housing | 3D-printed (coming soon) | *varies* |
| Lenses | fresnel lenses | 2 |

So, a little on our recommendations:

The Ardiuno Pro Micro is a good choice since it supports USB HID. The USB HID class is generally more optimized for smaller, more frequent packets of data vs. serial, which is better for our use since we'll be sending small but very frequent data from our IMU. Other devices, like the Pro Mini and Uno, all use the serial class, which isn't optimal, as we mentioned.

In terms of IMUs; While [FastIMU](https://github.com/LiquidCGS/FastIMU) (what we'll be using to read IMU data) supports the MPU-6050 (which is cheaper), it doesn't include a magnetometer, which could negatively affect overall tracking quality. Given that the MPU-9250 is nearly the same price on Aliexpress and Amazon, we figure most people can just purchase the better IMU.

In terms of displays, there are two primary options individuals often end up purchasing on Aliexpress. This one [(1)](https://www.aliexpress.com/item/1005003041935114.html?spm=a2g0o.productlist.main.1.1d772bcaTyAcB7&algo_pvid=efb5f8ad-1c86-4143-a5a5-f89fa8cfbcf9&algo_exp_id=efb5f8ad-1c86-4143-a5a5-f89fa8cfbcf9-0&pdp_npi=4%40dis%21CAD%21165.17%21135.43%21%21%21118.75%21%21%402101e7f617023300085442577ef149%2112000023407618642%21sea%21CA%212846674746%21&curPageLogUid=faw9rYRIEwGB) is what we picked. It is one of the cheapest displays on the market that has a decently high resolution (1440x1440, akin to a Valve Index) and a refresh rate at 90Hz (unlike the Index's 120Hz).

<div>
<img src="images/guide/1440p_option.png" alt="1440p display option" style="width: 40%; height: auto;"> <br>
<figcaption><em>Here's what the listing for this one looks like, complete with the driver board and 2 displays. Usually sells for ~$150 CAD.</em></figcaption>
    </div>
    <br>

<div>
<img src="images/guide/displays.jpg" alt="dual displays for VR" width = "50%" height = "auto"> <br>
<figcaption><em>Here is what it looks like in person.</em></figcaption>
    </div>
    <br>

This one [(2)](https://www.aliexpress.com/item/32979565265.html?spm=a2g0o.productlist.main.15.1d772bcaTyAcB7&algo_pvid=efb5f8ad-1c86-4143-a5a5-f89fa8cfbcf9&algo_exp_id=efb5f8ad-1c86-4143-a5a5-f89fa8cfbcf9-7&pdp_npi=4%40dis%21CAD%21113.19%2181.49%21%21%2181.38%21%21%402101e7f617023300085442577ef149%2166830344085%21sea%21CA%212846674746%21&curPageLogUid=IfaiMALuewVh) is a slightly more expensive but also great option that brings OLED to your HMD. It is at a slightly lower resolution however, coming in at 1080x1200, and it is 3.81 inches (versus the 2.9 inch 1440p display from the first option), bringing it's effective sharpness even lower. But, it does still have 90Hz. The specifications are overall very similar to the Oculus CV1's.

<div>
<img src="images/guide/1080p_oled_option.png" alt="1080p OLED display option" style="width: 40%; height: auto;"> <br>
<figcaption><em>Here's what the listing for the OLED one looks like, with the driver board and 2 displays. This is usually around ~$220 CAD.</em></figcaption>
    </div>
    <br>

The folks over at Smart Prototyping also have a 1440x1600 120Hz display built for Project Northstar (an open-source AR device), which oftentimes can be found for cheaper through B-stock options. Find them [here](https://www.smart-prototyping.com/AR-VR-MR-XR/Display-for-Project-North-Star-3_5inch-1440x1600-pixels%20Promotion%20sale).

Make sure that when purchasing any of these displays, you (1) purchase both screens and the driver board, you (2) make sure the driver board has the displays extend and not duplicate, and you (3) also purchase a long, compatible HDMI 2.1 cable and a USB Micro-B to A (or C) cable for the Arduino. These two cables will be thethering you to your computer, so make sure they're of decent quality as well (watching for durability/flexibility).

In terms of lenses, PMMA plastic (acrylic) fresnels are the easy pick. They are absolutely everywhere, are cheap, and are lighter/more compact than traditional biconvex glass lenses. Since they are everywhere, you can pick a diameter and focal length that you want. However, we went with 50mm focal length and 50mm diameter lenses, and they are what our 3D printed design is based around. You could also very well purchase a phone VR kit from Amazon or a store and put in your displays - that would work fine, but you would not know what kind of lenses you are getting.

If you would like to use our 3D printed design, make sure you are using 50mm diameter / 50mm focal length lenses, preferrably biconvex glass (as these are what we used to design the housing, and are thicker than equivalent fresnel lenses). You can find the print design to download in the Reality from Scratch housing folder (coming soon).

### Beginning the build

We can begin by building out the brains of the HMD. This will take no time and be useful to test the drivers, MCU, and IMU before purchasing and/or putting everything else together. This begins by taking your Arduino Pro Micro and connecting the following 4 pins from your Pro Micro to your IMU:

> GND $\longleftrightarrow$ GND (Ground) <br>
> VCC $\longleftrightarrow$ VCC (Voltage) <br>
> SDA $\longleftrightarrow$ SDA (Serial Data) <br>
> SCL $\longleftrightarrow$ SCL (Serial Clock) <br>

<div>
<img src="images/guide/pro_micro_pinout.png" alt="Arduino Pro Micro Pinout (SparkFun)" style="width: 40%; height: auto;"> <br>
<figcaption><em>Example SparkFun Arduino Pro Micro Pinout. GND, VCC, SDA, and SCL must be connected.</em></figcaption>
    </div>
    <br>

Download/open the Arduino IDE on your PC and connect your Pro Micro using the USB Micro-B to USB-C cable. In order to upload the firmware, you will need the [FastIMU](https://github.com/LiquidCGS/FastIMU/tree/main) library (and the RF24 Arduino Library if you're using HadesVR's HMD PCB with the RF transcievers). You can download these through the Library Manager on the Arduino IDE. Upload the "Calibrated_HadesVR.ino" file or the "Calibrated_Relativty.ino" sketch to the Arduino Pro Micro. Calibrate your IMU by following the steps shown in the console.

Now, you can install the drivers so that SteamVR can recognize the HMD. Place the "realityfromscratch" drivers (which you can download [here](/drivers/)) within the SteamVR "drivers" folder.

## WIP (for now)! If you need a complete guide, find Relativty's or HadesVR's.