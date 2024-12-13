# Root and Install Android 6 on 1st Edition Kindle Fire

1. What you need:
	- First Edition Kindle Fire (2011 model)
	- Micro USB cable (Data, not just power!)
	- Linux computer (Ubuntu, Debian, and Kali confirmed)
	- kindleRaA.zip (Provided)
2. Enable Allow Installations from Unknown Sources on Kindle (under settings devices)
3. Root the Kindle
	- Unzip kindleRaA.zip
	- Open terminal inside the `main` folder
	- Install requirements 
	- `sudo apt install adb fastboot busybox`
	- Prep script 
	- `chmod +x install.sh`
	- Run script 
	- `./install.sh`
	- Follow on screen instructions
4. Set up recovery
	- Send fbmode to the Kindle 
	- `adb push fbmode /data/local/tmp`
	- Set permissions 
	- `adb shell chmod 755 /data/local/tmp/fbmode`
	- Open the shell 
	- `adb shell`
	- Become root 
	- `su`
	- Run fbmode 
	- `cd /data/local/tmp`
	- `./fbmode` 
	- Now `exit` twice to get out of the shell
	- Reboot the Kindle 
	- `adb reboot`
	>Note, it will only boot to the kindle logo now
1. Set up TWRP
	- Flash TWRP 
	- `fastboot flash recovery otterx-recovery-TWRP-2.8.5.0.img`
	- Flash bootloader 
	- `fastboot flash bootloader otterx-u-boot_v2.05.bin`
	- Set bootmode 
	- `fastboot oem idme bootmode 4000`
	- Reboot 
	- `fastboot reboot`
1. Setup partitions
	- Open the bootloader menu by pressing [POWER]
	- Use 1-2 SHORT presses of [POWER] to highlight "ADVANCED -->"
	- Use a LONG press of [POWER] to select ADVANCED MENU
	- Use 5 SHORT presses of [POWER] to highlight "PARTITION MODE: AMAZON" (if this is OTTERX you can skip step 6)
	- LONG press [POWER] to open "PARTITION MODE MENU"
	- Use 4 SHORT presses of [POWER] to highlight "[CONFIRM] SET MODE: OTTERX"
	- LONG press will select this option and your system, cache, userdata will be changed to the OtterX layout (and ERASED)
	- Now use short [POWER] presses to navigate BACK and then to "RECOVERY"
	- Once in TWRP:
	- [Main Menu] Wipe -> Advanced -> check "Cache" and Swipe
	- [Main Menu] Reboot -> Recovery (wait for it to come back into TWRP)
	- [Main Menu] Wipe -> "Format Data", type "yes" and hit enter
	- [Main Menu] Wipe -> Advanced -> check "System" and Swipe
2. Push ROMS
	- Enter recovery mode (If you are already in TWRP then skip this step)
	- `adb push ResurrectionRemix-M-v5.7.4-20161010-otterx.zip /sdcard/`
	- `adb push temp_touch_fix.zip /sdcard/`
	- In TWRP go to install
	- Select ResurrectionsRemix
	- Select add more zips
	- Select temp_touch_fix
	- Slide to confirm (Should see RR logo after)
    - Clear cache
    - Reboot system
3. Once booted, enable aggressive out-of-memory killer:
    - Open Kernel Auditor app
    - Grant root access
    - Go to Low Memory Killer
    - Apply "Very Aggressive" setting on boot.
    - Enable Airplane mode.
4. Fix Wifi
	- `adb root` 
	- If this fails, unplug and replug your Kindle, then try again while unlocked. A popup should appear asking for debug permission, hit allow. Should get output "restarting adbd as root"
	- `adb remount`
	- `adb shell`
	- `nano /system/xbin/fix-mac.sh`
	- on line 20, replace `setprop wifi.ready 1 && \` with `insmod /system/lib/modules/wl12xx_sdio.ko && \`
	- On lines 30 and 31, add `toybox` in front of `sed` like this: `lowbytes=$(echo "$MACADDR" | toybox sed -e 's#^(..)` `highbytes=$(echo "$MACADDR" | toybox sed -e 's#^(..)`
	- On line 49, replace: `setprop wifi.ready 1` with `insmod /system/lib/modules/wl12xx_sdio.ko`
	- Save and exit
	- Make sure `/data/local/userinit.sh` looks like this. If it doesn't exist, create it.
		`#!/system/bin/sh`
		`/system/xbin/fix-mac.sh`
		(No space in front!)
	- Save and exit
	- Exit 
	- `exit`
	- Unroot 
	- `adb unroot`
1. Grant root access to apps
	- Install initd 
	- `adb install Universal-initd.apk`
	- Open Universal initd app
    - Tap "Test"
    - Grant root access
    - Turn on the service
    - Reboot
2. Install F-Droid (optional)
	- `adb install F-Droid.apk`
3. Connect to Wi-Fi using settings
4. Done!

>Sources: Low quality Youtube videos and ancient XDA forums
