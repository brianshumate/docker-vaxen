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

This will be a very brief guide to installing OpenVMS VAX 7.2 on the VAX 8650, as other more qualified documentation is available. Check the Resources section for more information.

At the `sim>` prompt, boot from the CD-ROM.

```shell
boot rq3
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
