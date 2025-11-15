# Docker VAXen

This project uses [Open SIMH](https://opensimh.org/) in a Docker container to emulate a VAX 8650 series minicomputer.

## Prerequisites

- Docker
- [OpenVMS VAX 7.2 ISO](https://archive.org/details/compaq-hp-openvms-vax-7.2-hobbyist-edition-version-2.0)

## Get started

1. Clone this repository.

    ```shell
    git clone https://github.com/brianshumate/docker-vaxen.git
    ```

1. Change into the repository directory.

    ```shell
    cd docker-vaxen
    ```

1. Grab the [OpenVMS 7.2 ISO](https://archive.org/details/compaq-hp-openvms-vax-7.2-hobbyist-edition-version-2.0), and place it in the `iso` directory as the file `VAXVMS072.iso`.

1. Run the container with one of the following methods.

### Run the container with docker run

Use this example command line toun the container with `docker run`.

```shell
docker run \
  --name vax8600 \
  --hostname vax8600 \
  --interactive \
  --tty \
  --cap-add=NET_ADMIN \
  --cap-add=NET_RAW \
  --env=TERM=xterm-256color \
  --publish 8600:8600 \
  --volume ./bin/:/vax8600/bin \
  --volume ./data:/vax8600/data \
  --volume ./logs:/vax8600/logs \
  --volume ./iso:/vax8600/iso \
  --volume ./vax8600.ini:/vax8600/vax8600.ini \
  --rm \
  brianshumate/vax8600:latest
```

### Run the container with docker compose

If you prefer to use the Docker Compose file, you can run the container with `docker-compose run` instead.

```shell
docker compose run --rm vax8600
```

## Install OpenVMS VAX 7.2

The following is a brief guide to installing OpenVMS VAX 7.2 on the VAX 8650, as other more qualified documentation is available. Check the Resources section for more information.

1. At the `sim>` prompt, boot from the CD-ROM to start the installation process.

    ```shell
    boot rq3
    ```

1. The system prompts you to enter the current date and time. Enter the date and time in the format `DD-MMM-YYYY HH:MM`, a somewhat nonintuitive format where the month is abbreviated to three letters. For example, `01-JAN-2023 12:00`.

    ```shell
    PLEASE ENTER DATE AND TIME (DD-MMM-YYYY  HH:MM)
    15-NOV-2025 16:19
    ```

    After you enter the date and time, the system begins configuring devices.

1. Eventually a device list appears like this example:

    ```plaintext
    Available device  DUA0:                            device type RA92
    Available device  DUA1:                            device type RD54
    Available device  DUA2:                            device type RD54
    Available device  DUA3:                            device type RRD40
    Available device  MTA0:                            device type TE16
    Available device  MTA1:                            device type TE16
    Available device  MTA2:                            device type TE16
    Available device  MTA3:                            device type TE16
    Available device  MTA4:                            device type TE16
    Available device  MTA5:                            device type TE16
    Available device  MTA6:                            device type TE16
    Available device  MTA7:                            device type TE16
    ```
    
    Enter `yes` to continue.
    
1. The system enters backup mode as evidenced by a prompt like this example:

    ```plaintext
    %BACKUP-I-IDENT, Stand-alone BACKUP T7.2; the date is 15-NOV-2025 15:51:55.73
    ```

    Enter the following command to restore the VAX/VMS image from the CDROM to the disk:
    
    ```shell
    backup dua3:vms072.b/save_set dua0:
    ```
    
    After restoration, the system prompts you to restore additional images. You can stop the whole emulation/simulation session at this point by pressing `CTRL+E`.

1. At the `sim>` prompt, boot from the disk instead of the CDROM:
    
    ```shell
    boot rq0
    ```

1. Now you should notice a nice banner with OS details, and once again, the system prompts you for the date and time. Enter the current date and time when prompted.

    ```shell
    %SYSBOOT-I-SYSBOOT Mapping the SYSDUMP.DMP on the System Disk
    %SYSBOOT-W-SYSBOOT Can not map SYSDUMP.DMP on the System Disk
    %SYSBOOT-I-SYSBOOT Mapping PAGEFILE.SYS on the System Disk
    %SYSBOOT-I-SYSBOOT SAVEDUMP parameter not set to protect the PAGEFILE.SYS
       OpenVMS (TM) VAX Version BI72-72T Major version id = 1 Minor version id = 0
    
    
               OpenVMS VAX V7.2 Installation Procedure
    
                             Model: VAX 8650
                     System device: RA92 - _DUA0:
                       Free Blocks: 2862468
                          CPU type: 04-00
    
    
    
    * Please enter the date and time (DD-MMM-YYYY HH:MM) 15-NOV-2025 16:01
    ```

    After you enter the date and time, the system continues to boot.

1. The system prompts you for a disk label for the hard disk. It is common practice to choose a short name and append `SYS` to it. For example, if you choose `VAX8650`, the disk label would be `VAX8650SYS`.

1. The system prompts you for the disk with the installation media.

    Enter `dua3` and press return.

1. The system asks if the media is ready to be mounted.

    Enter `yes` and press return.

1. The system presents the list of software to install.

    ```plaintext
    %MOUNT-I-MOUNTED, VAXVMS072 mounted on _DUA3:
    
        Select optional software you want to install.  You can install one
        or more of the following OpenVMS or DECwindows components:
    
        o OpenVMS library                              -  52200 blocks
        o OpenVMS optional                             -  19000 blocks
        o OpenVMS Help Message                         -  10400 blocks
        o OpenVMS Management Station                   -  20000 blocks
        o DECwindows base support                      -   4400 blocks
        o DECwindows workstation support               -  23800 blocks
              -  75 dots per inch video fonts          -    (included)
              - 100 dots per inch video fonts          -   6200 blocks
        o DECnet-Plus networking                       -  80000 blocks
        o DECnet Phase IV networking                   -    800 blocks
    
        Space remaining on system disk:  2862189 blocks
    ```
    
    - The system asks if you want to install the OpenVMS library files. Answer with `y` and press return.
    
    - The system asks if you want to install the OpenVMS optional files. Answer with `y` and press return.
    
    - The system asks if you want to install the MSGHLP database. Answer with `y` and press return.
    
    - The system will ask for a location to install the MSGHLP database. Press return to accept the default location.
    
    - The system will ask if you want to install DECwindows base support. Answer with `y` and press return.
    
    - The system will ask if you want to install DECwindows workstation support. Answer with `y` and press return.
    
    - The system will ask if you want to install 100 dots per inch video fonts. Answer with `y` and press return.

    - The system will ask if you want to install DECnet-Plus. NOTE: DECnet-Plus is no longer available in the hobbyist edition of OpenVMS. You can use CMU TCP/IP instead. Answer with `n` and press return.
   
    - The system will ask if you want to install DECnet Phase IV. Answer with `n` and press return.
    
    - The system will ask if your selections are correct. Answer with `y` and press return.
    
    - The system prompts if you want DECwindows Motif as the default windowing system. Answer with `y` (for nostalgia's sake lol) and press return.

1. Now it's time for you to set passwords on the important accounts:

    - SYSTEM: the "root" of VMS
    - SYSTEST: the debugging account
    - FIELD: the system maintenance account
    
    Enter and confirm passwords for each account.
    
1. The system prompts you for the SCSNODE name. You can enter a meaningful hostname-like value here. The name must be 6 or fewer alphanumeric characters with at least one alphabetic character.


1. The system prompts you for the SCSSYSTEMID. This is a numeric value that identifies your system. It must be an integer between 1025 and 65535.

1. The system prompts for your location. Choose the appropriate number for the location you want and confirm your selection by pressing <mono>return</mono>.

1. Choose your time zone and confirm your selection by pressing `return`.

1. If you are in daylight savings time, choose the appropriate option and confirm your selection by pressing <mono>return</mono>. Confirm your values are correct by pressing `Y`.

The system now restarts, and becomes available for use.

```plaintext
Welcome to OpenVMS (TM) VAX Operating System, Version V7.2

Username:
```

## Configure OpenVMS

TBD...

## Resources

- [OpenVMS VAX Version7.3 Upgrade and Installation Manual](/pdf/AA-QSBQD-TE.pdf)
- [open-simh code](https://github.com/open-simh/simh)
- [VAX Simulator (SimH)](https://www.openvmshobby.com/vax-vms/openvms-on-vax-simh/) tutorial by OpenVMS Hobby
- [OpenVMS on a Raspberry Pi](https://blog.poggs.com/2020/04/21/openvms-on-a-raspberry-pi/)
- [Installing VMS in SIMH on Linux](https://vanalboom.org/node/18.html)
- [OpenVMS VAX Version 7.1 Upgrade and Installation Manual](https://www0.mi.infn.it/~calcolo/OpenVMS/ssb71/6487/6487p010.htm)
- [VaxHaven CD image archive](https://www.vaxhaven.com/CD_Image_Archive)
