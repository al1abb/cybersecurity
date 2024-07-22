# Getting started with CMD

## Part 1

1. done
2. done
3. Navigation done
4. Use `mkdir` to create folder
5.  Create a text file using&#x20;

    ```
    type nul > myfile.txt
    ```
6. `notepad myfile.txt`
7. `type myfile.txt`  to get content of file
8. Use `ren` to rename file

`ren oldname newname`

9. `dir *.jpg /s`

Use this to search for all files with jpg extension. Do this from C: Drive

## Part 2

1. `ipconfig /all`

IPv4: 192.168.75.128(Preferred)

DNS Suffix: localdomain

2. `findstr`

`ipconfig /all | findstr IPv4` - Show your IP Address

3. `netstat -anb`

`netstat -anb | findstr TCP` - write down the TCP listening ports

4. `systeminfo | findstr KB`

7 KBs are installed. KB5039884 is .NET Framework 4.8 Patch

5. `sc query | findstr Defender`

4 services are assigned to defender
