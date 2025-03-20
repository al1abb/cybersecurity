# 02 - Introduction to Windows 10 and Computer Systems

Computer System

Includes hardware and software

## Boot Process

Includes 4 main steps:

• Powering up&#x20;

• POST&#x20;

• Finding a boot device&#x20;

• Loading the operating system

### POST

• Power on Self Test&#x20;

• The process of the BIOS checking all the essential hardware, when the computer is booted&#x20;

• POST makes sure the hardware is working properly

• If a device is malfunctioning, POST will alert it with series of beeps&#x20;

• Different manufacturers use different beep codes – the information can be found in the motherboard manual

• A POST card can be used to help debugging POST problems&#x20;

• The card monitors the boot process and reports errors&#x20;

• The errors are reported as coded numbers on a LED panel on the card

• The POST card is installed in an expansion slot on the motherboard

• To determine if POST is working properly, remove all the RAM modules from the computer and power it on&#x20;

• The computer should emit the beep code for a computer with no RAM installed&#x20;

• This will not harm the computer

## BIOS

Basic Input/Output Unit

The first thing that load when you start your computer.

The BIOS is a ROM chip on the motherboard. The BIOS is a physical chip (hardware) that has a software built into it. It contains a small program that controls the communication between the operating system and the hardware

Responsible for:

• Starting the computer – boot process&#x20;

• Managing essential devices before the OS is launched&#x20;

• Managing motherboard settings

• Some aspects of the BIOS can be configured manually&#x20;

• The configuration data is saved to a special memory chip called CMOS

{% hint style="info" %}
BIOS is a firmware. Firmware is combination of hardware and software
{% endhint %}

### CMOS

• Complementary Metal-Oxide Semiconductor&#x20;

• Stores the startup configuration of a PC for many years&#x20;

• In the past, CMOS was a separated chip on the motherboard, nowadays it’s integrated into the southbridge

• The settings are retained by CMOS using a lithium battery&#x20;

• If the battery dies, all BIOS setup configuration data is lost

### UEFI

• Unified Extensive Firmware Interface&#x20;

• UEFI is an upgraded BIOS - it performs the same tasks performed by the BIOS and more&#x20;

• UEFI is designed to eventually make BIOS obsolete

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>BIOS vs UEFI</p></figcaption></figure>

## Operating Systems

An operating system (OS) is software that controls a computer

### 4 main functions of an OS

• Managing hardware – UEFI/BIOS, managing memory, drivers&#x20;

• Managing files and folders – creating, storing, retrieving, deleting and moving files&#x20;

• Managing applications – installing/uninstalling and running apps&#x20;

• Providing a user interface – providing a way for the user to manage the desktop, hardware, apps and data

## User Interface

There are 2 types of UI: CLI and GUI

## Microsoft Windows

### Windows 10 Editions

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

| Edition               | Description                                                                       | Consumer                                             | Availability                                     |
| --------------------- | --------------------------------------------------------------------------------- | ---------------------------------------------------- | ------------------------------------------------ |
| Windows 10 Home       | Consumer oriented desktop edition Windows 10 Home                                 | Individual/home users                                | Everybody                                        |
| Windows 10 Pro        | Supports The needs of a smallmedium business                                      | Organizations, advanced users                        | Everybody                                        |
| Windows 10 Enterprise | Similar to Windows 10 Pro, but it adds few business-based features                | Large enterprises                                    | Only available to Volume Licensing customers     |
| Windows 10 Education  | Offers the same features as Windows 10 Enterprise                                 | School staff, administrators, teachers, and students | Only available through academic Volume Licensing |
| Windows 10 Mobile     | For smaller, mobile, touch-centric devices, such as smartphones and small tablets | Individual users with small devices                  | Everybody                                        |

### 32-bit vs 64-bit

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Hardware requirements

• Processor: 1 GHz or faster&#x20;

• RAM: 1 GB for 32-bit or 2 GB for 64-bit&#x20;

• Hard disk: 16 GB for 32-bit or 20 GB for 64-bit&#x20;

• Graphics card: Microsoft DirectX 9 graphics device with WDDM 1.0 driver&#x20;

• Display: 800x600

• Windows Hello - requires biometric hardware&#x20;

• Secure boot - requires firmware that supports UEFI&#x20;

• BitLocker - requires TPM or a USB flash drive&#x20;

• Client Hyper-V - requires a 64- bit system with second-level address translation capabilities and an additional 4 GB of RAM and CPU support for VM Monitor Mode Extension

### Configuration Tools

#### GUI tools

Settings App (Introduced in Win 8) or Control Panel (Introduced in Win 2.0)

{% hint style="info" %}
Settings app is quickest way to make configuration changes

Control Panel is more advanced than Settings App
{% endhint %}

#### CLI Tools

CMD - Command Prompt

PowerShell - designed for system administration, developed by MS

{% hint style="info" %}
PowerShell is more powerful than CMD
{% endhint %}

#### Advanced Tools

• Registry&#x20;

• Group policy&#x20;

• Administrative tools

## Configuring Network Connectivity

**DNS (Domain Name System)** - translates names to IP addresses

**NIC (Network Interface Card)** - a hardware that connects the device to a network

## Configuring Devices

When you install a new device, both the device and the device drivers must be installed.

### Device Drivers

**Drivers** - group of files (software) that enable hardware to communicate with the OS

{% hint style="info" %}
Without drivers the computer won’t be able to send/receive data to/from the hardware
{% endhint %}

{% hint style="info" %}
Today’s OS have a lot of generic drivers that allow hardware to work on a basic level without needing drivers or software
{% endhint %}

• If the device has features unknown to the OS it will not work and you’ll have to install it&#x20;

• You can download the drivers you need from the manufacturer’s website
