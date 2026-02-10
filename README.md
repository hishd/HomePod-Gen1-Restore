# Project: HomePod Restore

First things First, a Huge thanks goes to [Tihmstar](https://github.com/tihmstar) and [UnbendableStraw aka Nic’s Fix](https://github.com/UnbendableStraw) for comming up with this amazing approach from building a custom adapter and flashing firmware to restore HomePod Gen 1s.


## Overview

This repository is a guide for you to restore a HomePod (Generation 1), if it suffers fom a boot loop with blinking volume up and down buttons.

**The Cause:**
This bootlooping issue might occur due to a software update failure during the update process and therefore a corrupt file system or boot partition. Mine broke while updating to version 26.2.

**The Fix:**
The fix is actually very simple, all we need to restore it. But this process is not as straightforward as the HomePod Mini's. I also have some boot looping issues on my Black and White HomePod Mini's and was able to restore them easily by just pluging them to my MacBook. But on HomePods like HomePod 1 and HomePod 2 we don't have any USB-C ports. So we have to rely on the provided debug pins (as I call them debug pins) at the bottom inside the plastic cover.

**Less talk, more work now, shall we....????**

---

## First things First

We need to remove the bottom cover of the HomePod. It's easy to remove, no heat needed, just use your fingers and try to lift around and it will come off eventually. After that you will see the debug pins (the golden contact pads) that I was talking about earlier.

Please note that you need the below tools and equipment to continue with restoring the HomePod as we have to make an adapter from scratch to connect with the debug port. Making the adapter is easy, all we need is the below set of tools and a little bit of soldering skills. All the below tools, I bought them for cheap from Aliexpress (~ 15GBP or ~20 USD).

** Note: If you want, you can also purchase an adapter from Nic’s Fix using the link: https://nicsfix.com/shop/. I decided to make my own as the shipping will take ages to arrive.

1. A Glue Gun (I bought a 20W one).
2. Soldering Iron (I bought a 60W one).
3. Some glue sticks and solder wire.
4. Long Pin Header 2.54mm Female.
5. Male Pin Header Single Row (L Shaped)
6. A USB cable (short one would be nice)
7. MacOS device (I used an Apple Silicon based MacBook)

To make the adapter we need 4 column Long Pin Header row and a 4 column Male Pin header row. I will attach some images for your reference.

---

## Making the Adapter

Have a look at the below image (**credits: Nic’s Fix**):

![credits: Nic’s Fix](https://github.com/UnbendableStraw/homepwn-simple/blob/main/debug.jpg?raw=true)

The above diagram explains the connectivity of the USB wiring which we will be making next.

You can follow the below steps to make the adapter from the above tools.

1. First cut the USB cable in half, you can use the Type-C end or the USB-A end, it depends what ports you have on your Mac (I used the Type-A end with a USB-C Adapter to plug it into my Mac).
2. Strip the end of the wires of the USB cable, you need to strip the Black, White, Green and Red wires. Ignore the yellow wire if you have one (it only used to enable some high bandwidth connection which we don't need).
3. Add some solder to those wires so that it's easy for use to solder the wired into the pins.
4. Follow the above image and solder the wires according to that order (you must follow the order) into the L shape 4 column Male Pin header row (refer images below).
5. Put some hot glue into the wires so that they can be secured without moving. Make sure to use only a small amount of hot glue. If you use a lot of glue, the assembled adapter won't fit into the debug port.
6. Grab the 4 column Long Pin Header row and bend the pins to form a L shape (refer images below), this will let us tap and connect into the debug pins on the HomePod.
7. Now, plug that L shape 4 column Male Pin header row (which has the soldered USB wire) into the 4 column Long Pin Header row (refer  images below).
8. Align those pins with the HomePods debug pads and cross check with the above diagram. The order should be **Black, White, Green and Red** from the **RIGHT** while the AC plug is on the top of the HomePod.
9. **DOUBLE CHECK AGAIN THE WIRING ORDER WITH DIAGRAM**.
10. Now we can hold the adapter and add some hot glue to attach the adapter onto HomePod. Don't put too much glue, as it will be a bit hard to remove the adapter afterwards.


## Let's Restore our HomePod

Here on we need some work on our MacOS device. I attempted the repair as of 10.02.2026 on my MacBook Pro running MacOS 15.7.3 so when you are using the below commands on the terminal, those utilities, packages and tool versions might be different and depricated and it might give you some warnings or errors.

I also faced some issues while installing ```libimobiledevice-glue``` and running ```make``` command according to the original guide, so I had to find a fix, which I will include below, if you also face the same error.

The below is the steps of the original guide which you can find here: https://github.com/UnbendableStraw/homepod-restore/

Steps:

1. brew tap d235j/ios-restore-tools
2. brew install --HEAD libimobiledevice-glue
3. brew install --HEAD d235j/ios-restore-tools/libimobiledevice
4. brew install --HEAD libirecovery
5. brew install --HEAD gaster
6. brew install --HEAD ldid-procursus
7. brew install libzip
8. git clone https://github.com/libimobiledevice/idevicerestore.git 
9. cd idevicerestore
10. git checkout d2e1c4f
11. ./autogen.sh
12. make
13. sudo make install

 
 **The issues I faced:**

i. When executing `brew install --HEAD libimobiledevice-glue` I got an error,

    ==> Pouring m4--1.4.21.arm64_sequoia.bottle.tar.gz 
    Error: No such file or directory @ rb_sysopen - /Users/hishd/Library/Caches/Homebrew/

So I had to fix this issue by doing,

1. brew cleanup --prune=all
2. rm -rf ~/Library/Caches/Homebrew
3. brew update
4. brew install --build-from-source automake libtool libplist
5. brew install --HEAD libimobiledevice-glue --build-from-source

ii. When running ```make``` command I again got an error (this is due to some depricated package/ version error as I explained earlier)

Again the fix is easy, you have to go to the idevicerestore/src directory and find the file named **dfu.c** and then open it in a editor like nano or VSCode. You will find a line called `irecv_init();` (it was on line 87 of mine). Just delete or comment out that line.

That's it. Now we can execute ```make``` and then ```sudo make install```

Now let's make sure we are ready to go further by ensuring our above utilities are ready by executing below on terminal,

`idevicerestore -v`

If you have successfully performed the installation this should return something like below (the versions might be different on yours),

    idevicerestore 1.0.0-215-gd2e1c4f (libirecovery 1.3.1, libtatsu 1.0.5)

### Now comes the interesting part

Now we are at the final step of restoring the HomePod. Perform the below steps.

1. Download the version 18.3 firmware for HomePod: [https://nicsfix.com/ipsw/18.3.ipsw](https://nicsfix.com/ipsw/18.3.ipsw).
2. Make sure you are on the directory `idevicerestore` not the `idevicerestore/src` or any other.
3. Move the downloaded file (18.3.ipsw) into your `idevicerestore` directory.

**Now make sure you carefully do the below steps in order (Order is MUST)**

1. Connect the USB-A or USB-C end of the cable into your MacOS device.
2. **After connecting the USB cable to your MacOS device**, you should connect the HomePod to AC power.
3. If connection is successful (if you made the adapter right) you will see a popup saying **Allow accessory to connect**. Allow it and you wil also see your HomePod in finder window (The Restore HomePod button will not work of course :D ).

**The Final Steps**

On your terminal execute the below.

1. ```gaster pwn```
2. ```gaster reset```
3. ```idevicerestore -d -e 18.3.ipsw```

During the flashing firmware process it will sometimes ask your permissions saying `Allow accessory to connect`, if asked press Allow.

You will also see logs like, `No data to read (timeout)` a lot, this is normal.

These will take around 10-15 minutes upto 20 minutes, so **BE PATIENT....!!!!!** Don't touch your Adapter or Don't do anything silly with the wires ;) it might cause a hard-brick on your HomePod and you will end up with an E-Waste.

If the operation is successful you will see something like below on your terminal.

    Got status message
    Status: Restore Finished
    ReverseProxy[Ctrl]: Terminating
    ReverseProxy[Ctrl]: (status=2) Terminated
    DONE
    [==================================================] 100.0%

Now you are done with successfully restoring your HomePod to version 18.3. Now you should perform below.

1. Power off the AC connection.
2. Remove the USB cable from the MacOS device.
3. Power on HomePod, **after removing the USB cable**.
4. Your HomePod will take some time to setup.
5. You can now connect your HomePod to your Home App as usual.
6. Once connected the Home App will start configuring your HomePod and it will download the version 26.2 or later and start the update process.

# We are done now

**Image References**

![](https://github.com/hishd/HomePod-Gen1-Restore/blob/main/1.homepod-gen1.jpg?raw=true)

![](https://github.com/hishd/HomePod-Gen1-Restore/blob/main/2.4-row-pins.jpg?raw=true)

![](https://github.com/hishd/HomePod-Gen1-Restore/blob/main/3.soldered-cable.jpg?raw=true)

![](https://github.com/hishd/HomePod-Gen1-Restore/blob/main/4.adapter-connected-to-pins.jpg?raw=true)

![](https://github.com/hishd/HomePod-Gen1-Restore/blob/main/5.adapter.jpg?raw=true)

![](https://github.com/hishd/HomePod-Gen1-Restore/blob/main/6.adapter-donnection.jpg?raw=true)

![](https://github.com/hishd/HomePod-Gen1-Restore/blob/main/7.conection.jpg?raw=true)

**References:**

1. https://www.reddit.com/r/HomePod/comments/1i596jx/homepod_1st_gen_stuck_on_spinning_white_circle/
2. https://github.com/UnbendableStraw/homepod-restore/
3. https://github.com/libimobiledevice/libimobiledevice-glue
4. https://github.com/libimobiledevice/idevicerestore

