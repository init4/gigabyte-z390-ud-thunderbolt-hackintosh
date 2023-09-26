# Gigabyte Z390-UD Vanilla Hackintosh Installation

![Hackintosh info](https://github.com/init4/gigabyte-z390-ud-thunderbolt-hackintosh/blob/catalina/img/info.png?raw=true)![Display info](https://github.com/init4/gigabyte-z390-ud-thunderbolt-hackintosh/blob/catalina/img/display.png?raw=true)

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
| | or (mood dependant) [Keychron K2](https://www.keychron.com/products/keychron-k2-wireless-mechanical-keyboard) |
| Mouse | Apple Magic Trackpad 2 |

![Part List](https://github.com/init4/gigabyte-z390-ud-thunderbolt-hackintosh/blob/catalina/img/hardware.jpg?raw=true)

## Targeted OS
#### macOS Mojave 
For this build I wanted to use Mojave, since I find it to be a little faster and System Integrity Protection in Catalina is annoying. 

## Pre-requisites
- Get macOS Mojave (10.14.6) (see notes) 
- [Clover Install Package](https://sourceforge.net/projects/cloverefiboot/)
- [Clover Configurator Install Package](http://mackie100projects.altervista.org/download-clover-configurator/)
- The [EFI](https://github.com/init4/gigabyte-z390-ud-thunderbolt-hackintosh/tree/monterey/EFI) area from this repo

## Installation process 
1. Build the USB Installer disk. Read the [Vanilla](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/building-the-usb-installer) guide
    - Note that all guides seem to recommend a 16Gb USB drive (some even 32Gb!), I used 8Gb with no problems. FYI the Mojave install image is 5.7Gb on the USB drive. A USB3 drive will speed things up enormously 
    - Create the installer `sudo "/Applications/Install macOS Mojave.app/Contents/Resources/createinstallmedia" --volume /Volumes/USB`
2. Install Clover to USB Installer. Read the [Vanilla](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/clover-setup) guide
3. Clone this repo and copy the Clover files onto your USB Installer. E.g.:
    - `git clone https://github.com/init4/gigabyte-z390-ud-thunderbolt-hackintosh.git` 
    - Locate SSD & USB device `diskutil list`
    - Mount USB EFI partition `sudo mkdir /Volumes/efiusb`
    - `sudo mount -t msdos /dev/disk{number} /Volumes/efidisk`
    - Remove existing Clover stuff `rm -rf /Volumes/efiusb/EFI/CLOVER/*`
    - Copy my repo config on `cp -r ./gigabyte-z390-ud-thunderbolt-hackintosh/EFI/* /Volumes/efiusb/EFI/` 
4. Use Clover Configurator to edit config.plist, and generate real serial numbers/uuid so iMessage/etc will work 
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
    - Peripherals → Thunderbolt → Security: Disabled (IMPORTANT - see notes) 
    - Peripherals → Thunderbolt → USB: Enabled 
    - Power → Platform Power Management: Enabled 
    - Power → PEG ASPM: Disabled 
    - Power → PCH ASPM: Disabled 
    - Power → DMI ASPM: Enabled 
    - Power → ErP: Enabled 
7. Install macOS
    - I already had a Mojave image and ran into [this](https://9to5mac.com/2019/10/24/macos-application-damaged/) when I tried to install :roll_eyes: 
    - Fixed by setting the system date back to 2018 for the install (wont happen for a fresh download)  
8. Create EFI partition for Hackintosh
    - locate SSD & USB's disk no. by `diskutil list`
    - Create EFI partition for Hackintosh `sudo mkdir /Volumes/efidisk`
    - `sudo mount -t msdos /dev/disk{number} /Volumes/efidisk`
    - Mount USB's EFI partition `sudo mkdir /Volumes/efiusb`
    - `sudo mount -t msdos /dev/disk{number} /Volumes/efiusb`
    - Copy clover settings from USB `cp -R /Volumes/efiusb/* /Volumes/efidisk`
9. Enjoy, if you find any errors or this page helped get your Thunderbolt device going let me know 

## What works
Nearly everything works as far as I can tell; GPU, Network, Bluetooth, Audio, USB. iMessage works, GPU supports Metal, Homekit works. It was pretty painless.

## What doesn't work
The USB hub built-in to the Thunderbolt display will not wake from sleep. To wake it up again you must power cycle everything, including unplugging the screen for 30 seconds while the PC is off. For now I have just disabled sleep.

## Notes 
Get [macOS patcher](http://dosdude1.com/software.html) to make it easier to download the macOS install packages. Finding direct links to the full installer isn't easy.

If you are going to Hackintosh a Gigabyte Z390 UD, save yourself some time and upgrade the BIOS before you start, I wasted a couple of hours trying with F3 BIOS. Also, don't try to boot the installer from the front USB ports, this always failed in my experience. Note that the Gigabyte F9 and onward BIOS wont let you disable the SuperIO serial port (menu option is gone). This didnt seem to matter, even though others recommend disabling it. 

My research would indicate that Thunderbolt 1 & 2 don't implement security, and I couldn't make the Thunderbolt Display's in-built USB hub work while Thunderbolt security was enabled.

Also note that I have deliberately used the Alpine Ridge Thunderbolt 3 controller, because of the issues described [here](https://github.com/intel/thunderbolt-software-user-space/issues/66) it is doubtful that a Titan Ridge controller will ever work properly with an Apple Thunderbolt display.  

Once you have everything installed and set up properly, you can edit the Clover config.plist to remove verbose boot. This speeds up booting.

## Performance
Performance is similar to a real iMac Retina or iMac Pro, and would keep up with many Xeon Macs. Certainly build cost is far cheaper than a real Mac. As an experiment I will see what I can do with overclocking. 

![CPU Score](https://github.com/init4/gigabyte-z390-ud-thunderbolt-hackintosh/blob/catalina/img/geekbench-cpu-score.png?raw=true)

![GPU Score](https://github.com/init4/gigabyte-z390-ud-thunderbolt-hackintosh/blob/catalina/img/geekbench-gpu-score.png?raw=true)

## Todo 
- Overclocking
- Add some info on my software RAID setup

## References
My build was helped out by reading experiences from these guys, and messing with their Clover configs.

- [@marmartintsang](https://github.com/marmartintsang/gigabyte-z390-ud-hackintosh) 
- [@7gill](https://github.com/7gill/Gigabyte-Z390-UD-Catalina-install)
- [Coffee Lake Config](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/config.plist-per-hardware/coffee-lake)
 
