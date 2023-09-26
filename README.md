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
| Storage | Samsung SSD 980 EVO Plus 500GB M.2 Internal Solid State Drive |
| | 4x Western Digital 4TB SATA |
| Wifi | Fenvi T919 BCM94360CD |
| Bluetooth | Fenvi T919 BCM94360CD |
| Add-in card | Gigabyte Titan Ridge Dual Thunderbolt 3 |
| Screen | LG Ultrafine 5k 27-inch |
| Keyboard | [Keychron K2](https://www.keychron.com/products/keychron-k2-wireless-mechanical-keyboard) |
| Mouse | Apple Magic Trackpad 2 |

![Part List](https://github.com/init4/gigabyte-z390-ud-thunderbolt-hackintosh/blob/catalina/img/hardware.jpg?raw=true)

## Targeted OS
#### macOS Monterey 
This build was originally using Mojave, which I subsequently upgraded to Catalina. For more information on that configuration, please see the [catalina](https://github.com/init4/gigabyte-z390-ud-thunderbolt-hackintosh/tree/catalina/) branch. There were a few changes required to run Monterey.

## Pre-requisites
- Get macOS Monterey
- Opencore 0.9.4
- The [EFI](https://github.com/init4/gigabyte-z390-ud-thunderbolt-hackintosh/tree/monterey/EFI) folder from this repo

## Installation process 
1. Build the USB Installer disk. Read the [Dortania](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/) guide
    - You need a 16GB USB drive
    - Create the installer `sudo "/Applications/Install macOS Monterey.app/Contents/Resources/createinstallmedia" --volume /Volumes/USB`
2. Install Opencore to USB Installer. Read the [Dortania](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/opencore-efi.html) guide
3. Clone this repo and copy the Opencore files onto your USB Installer. E.g.:
    - `git clone https://github.com/init4/gigabyte-z390-ud-thunderbolt-hackintosh/tree/monterey/` 
    - Locate USB device `diskutil list` (typically, disk0 will be the boot drive, and USB will be something like disk2)
    - Mount USB EFI partition `sudo diskutil mount disk2s1`
    - Copy my repo config on `cp -r ./gigabyte-z390-ud-thunderbolt-hackintosh/EFI/* /Volumes/EFI/EFI/` 
4. Edit config.plist, and generate real serial numbers/uuid so iMessage/etc will work 
5. Update [BIOS](https://www.gigabyte.com/Motherboard/Z390-UD-rev-10/support#support-dl-bios), I used F9. 
6. Configure BIOS 
    - Load Optimized Default Settings
    - BIOS → Windows 8/10 Features: Other OS  
    - BIOS → CSM Support: Disabled 
    - Chipset → VT-d: Disabled
    - Chipset → Internal Graphics: Enabled
    - Chipset → Above 4G Decoding: Enabled
    - Peripherals → Intel Platform Trust Technology (PTT): Disabled
    - Peripherals → Legacy USB support: Enabled
    - Peripherals → XHCI Hand-off: Enabled
    - Peripherals → Port 60/64 emulation: Enabled
    - Peripherals → Network stack: Disabled 
    - Peripherals → Security device support: Disabled 
    - Peripherals → Super IO configuration → Serial port: Enabled (see notes) 
    - Peripherals → Thunderbolt → Security: Disabled
    - Peripherals → Thunderbolt → Boot: Boot once
    - Peripherals → Thunderbolt → USB: Enabled 
    - Power → Platform Power Management: Enabled 
    - Power → PEG ASPM: Disabled 
    - Power → PCH ASPM: Disabled 
    - Power → DMI ASPM: Enabled
    - Power → ErP: Enabled 
7. Boot the macOS installer via USB
8. Create EFI partition for Hackintosh
    - Locate SSD & USB's disk no. by `diskutil list` (typically, disk0 will be the boot drive, and USB will be something like disk2)
    - Mount SSD EFI partition `sudo diskutil mount disk0s1`
    - Mount USB EFI partition `sudo diskutil mount disk2s1`
    - Copy Opencore from USB to newly installed SSD `cp -R /Volumes/EFI/* /Volumes/EFI\ 1/`
9. Reboot

## Notes 
My GA-Z390-UD mainboard eventually died (RIP), so I wont be updating this repo any further. Aside from the normal challenges with Thunderbolt, the machine worked perfectly for 5+ years of 24x7 running.

Get [macOS patcher](http://dosdude1.com/software.html) to make it easier to download the macOS install packages. Finding direct links to the full installer isn't easy.

If you are going to Hackintosh a Gigabyte Z390 UD, save yourself some time and upgrade the BIOS before you start, I wasted a couple of hours trying with F3 BIOS. Note that the Gigabyte F9 and onward BIOS wont let you disable the SuperIO serial port (menu option is gone). This didnt seem to matter, even though others recommend disabling it.

Getting Thunderbolt hotplug working involved using a particular SSDT that was generated by [HackinDROM](https://hackindrom.zapto.org).

## Performance
Performance is similar to a real iMac Retina or iMac Pro, and would keep up with many Xeon Macs. Certainly build cost is far cheaper than a real Mac. As an experiment I will see what I can do with overclocking. 

![CPU Score](https://github.com/init4/gigabyte-z390-ud-thunderbolt-hackintosh/blob/catalina/img/geekbench-cpu-score.png?raw=true)

![GPU Score](https://github.com/init4/gigabyte-z390-ud-thunderbolt-hackintosh/blob/catalina/img/geekbench-gpu-score.png?raw=true)

## Todo 
- Overclocking
- Add some info on my software RAID setup
