# Gigabyte Z390-UD Vanilla Hackintosh Installation with fully working 27 inch Thunderbolt Display 

![Hackintosh info](https://github.com/init4/gigabyte-z390-ud-thunderbolt-hackintosh/blob/master/img/info.png?raw=true)

## Parts List
| Component     | Model         | 
| ------------- |:-------------:| 
| CPU | Intel® Core™ i9-9900K Processor | 
| | w/ Noctua NH-U12S cooler | 
| Motherboard | Gigabyte Z390 UD |   
| RAM | 4x Kingston, 16GB, DDR4, 2666MHz |
| Video Card | MSI Radeon® Vega 64 Air Boost 8GB | 
| | Displayport output passed back into TB3 Mini-Displayport input | 
| Storage | Samsung SSD 970 EVO Plus 500GB M.2 Internal Solid State Drive |
| | 4x Western Digital 4TB SATA |
| Wifi | None, I only need the wired network |
| Bluetooth | BCM20702 Bluetooth 4.0 USB |
| Add-in card | Gigabyte Alpine Ridge Dual Thunderbolt 3 |
| Screen | Apple Thunderbolt Display 27-inch |
| | Apple Thunderbolt 2 → 3 adapter |
| Keyboard | Apple Magic Keyboard |
| Mouse | Apple Magic Trackpad 2 |

![Part List](https://github.com/init4/gigabyte-z390-ud-thunderbolt-hackintosh/blob/master/img/hardware.jpg?raw=true)

## Targeted OS
#### macOS Mojave 
For this build I wanted to use Mojave, since I find the System Integrity Protection in Catalina to be annoying.

## Pre-requisites
- [Clover's Install Package](http://mackie100projects.altervista.org/download-clover-configurator/)

## Installation process 
1. Get `macOS Mojave (10.14.6)`
2. Building the USB Installer followed by [Vanilla](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/building-the-usb-installer) E.g: 
    - Note that all guides seem to recommend a 16Gb USB drive, I used 8Gb with no problems 
    - Create the installer `sudo "/Applications/Install macOS Mojave.app/Contents/Resources/createinstallmedia" --volume /Volumes/USB`
3. Install `Clover EFI bootloader` to USB Installer followed by [Vanilla](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/clover-setup)
4. Clone this repo and copy the Clover files onto your USB Installer. E.g.:
    - `git clone https://github.com/init4/gigabyte-z390-ud-thunderbolt-hackintosh.git` 
    - Locate SSD & USB device `diskutil list`
    - Mount USB EFI partition `sudo mkdir /Volumes/efiusb`
    - `sudo mount -t msdos /dev/disk{number} /Volumes/efidisk`
    - Remove existing Clover stuff `rm -rf /Volumes/efiusb/EFI/CLOVER/*`
    - Copy my repo config on `cp -r ./gigabyte-z390-ud-thunderbolt-hackintosh /Volumes/efiusb/EFI/CLOVER/` 
5. Edit the Clover config to include a real serial number so iMessage/etc will work 
5. Update [BIOS](https://www.gigabyte.com/Motherboard/Z390-UD-rev-10/support#support-dl-bios), I used F9. 
6. Configure BIOS 
    - Load Optimized Default Settings
    - BIOS → Windows 8/10 Features: Other OS  
    - BIOS → CSM Support: Disabled 
    - Chipset → VT-d: Disabled
    - Chipset → Internal Graphics: Auto
    - Chipset → Above 4G Decoding: Enabled
    - Peripherals → Intel Platform Trust Technology (PTT): Disabled
    - Peripherals → Legacy USB support: Enabled
    - Peripherals → XHCI Hand-off: Enabled
    - Peripherals → Port 60/64 emulation: Enabled
    - Peripherals → Network stack: Disabled 
    - Peripherals → Security device support: Disabled 
    - Peripherals → Super IO configuration → Serial port: Enabled (see notes) 
    - Peripherals → Thunderbolt → Security: Disabled (IMPORTANT! TB1/2 don't support security) 
    - Peripherals → Thunderbolt → USB: Enabled 
    - Power → Platform Power Management: Enabled 
    - Power → PEG ASPM: Disabled 
    - Power → PCH ASPM: Disabled 
    - Power → DMI ASPM: Enabled 
    - Power → ErP: Enabled 
7. Install macOS
    - I ran into this issue LOL [Apple signing key expired](https://9to5mac.com/2019/10/24/macos-application-damaged/) 
    - Fixed by setting the system date back to 2018 (or you can just download it again)  
8. Create EFI partition for Hackintosh
    - locate SSD & USB's disk no. by `diskutil list`
    - Create EFI partition for Hackintosh `sudo mkdir /Volumes/efidisk`
    - `sudo mount -t msdos /dev/disk{number} /Volumes/efidisk`
    - Mount USB's EFI partition `sudo mkdir /Volumes/efiusb`
    - `sudo mount -t msdos /dev/disk{number} /Volumes/efiusb`
    - Copy clover settings from USB `cp -R /Volumes/efiusb/* /Volumes/efidisk`
9. Enjoy, if this page helped get your Thunderbolt device going let me know 

## Notes 
If you are going to Hackintosh a Gigabyte Z390 UD, save yourself some time and upgrade the BIOS before you start, I wasted a couple of hours trying with F3 BIOS. Also, don't try to boot off the front USB ports, for some reason this would never work properly.

Note that the Gigabyte F9 and onward BIOS wont let you disable the SuperIO serial port (menu option is gone). This didnt seem to matter, even though others recommend disabling it. 

Everything works with one annoying exception; the USB hub attached to the Thunderbolt display will not wake from sleep. To wake it up again you must power cycle everything, including unplugging the screen for 30 seconds while the PC is off. For now I have just disabled sleep.

Also note that I have deliberately used the Alpine Ridge Thunderbolt 3 controller, because of the issues described [here](https://github.com/intel/thunderbolt-software-user-space/issues/66) it is doubtful that a Titan Ridge controller will ever work properly with an Apple Thunderbolt display.  

## Performance
Performance is better than a real iMac 5K, at less than a third of the build cost.

![CPU Score](https://github.com/init4/gigabyte-z390-ud-thunderbolt-hackintosh/blob/master/img/geekbench-cpu-score.png?raw=true)

![GPU Score](https://github.com/init4/gigabyte-z390-ud-thunderbolt-hackintosh/blob/master/img/geekbench-gpu-score.png?raw=true)

## References
My build was helped out by reading experiences from these guys, and messing with their Clover configs.

- [@marmartintsang] (https://github.com/marmartintsang/gigabyte-z390-ud-hackintosh) 
- [@7gill] (https://github.com/7gill/Gigabyte-Z390-UD-Catalina-install)
- [Coffee Lake Config](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/config.plist-per-hardware/coffee-lake)
 
