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

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption><p>BIOS vs UEFI</p></figcaption></figure>

## Operating Systems

An operating system (OS) is software that controls a computer

### 4 main functions of an OS

• Managing hardware – UEFI/BIOS, managing memory, drivers&#x20;

• Managing files and folders – creating, storing, retrieving, deleting and moving files&#x20;

• Managing applications – installing/uninstalling and running apps&#x20;

• Providing a user interface – providing a way for the user to manage the desktop, hardware, apps and data

## User Interface

