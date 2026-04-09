# Pi-tastic
Connect a Pi with [Meshtastic](https://meshtastic.org).

Initiated by Michel of [Lug Limbe](https://sokolo.cronopios.org/) ([Map](https://www.openstreetmap.org/search?query=limbe%20linux#map=19/4.01908/9.17187)).

## Overview
- A Pi computer sends messages to another Pi.
- They communicate over a LoRa mesh network.
- They use Xiao MCUs with LoRa radio modules.

Here is the final setup.

                       Sender                  Mesh Network                   Receiver
    
    [Pi Computer]-USB Serial-[Xiao MCU w/ LoRa]-----...---->[Xiao MCU w/ LoRa]-USB Serial-[Pi Computer]

## Pi computer
- Pi computer with Linux.
- Headless install is fine.
- We use a terminal via SSH.

On your computer, to log into the Pi.

    $ ssh pi@raspberrypi.local

## Xiao MCU w/ LoRa
- Seeed Xiao ESP32S3 MCU
- Wio SX1262 LoRa Module
- Connected to Pi via USB

On the Pi, to list USB devices.

    $ ls -al /dev/ttyA*
    ...
    crw-rw---- 1 root dialout ... /dev/ttyACM0

## Python with venv

On the Pi, set up a _venv_.

    $ python -m venv venv

Then, activate the _venv_.

    $ source venv/bin/activate

When done (not now).

    $ deactivate

As documented [here](https://packaging.python.org/en/latest/guides/installing-using-pip-and-virtual-environments/#create-and-use-virtual-environments).

## Install meshtastic

On the Pi, install _meshtastic_ CLI.

    $ pip3 install --upgrade "meshtastic[cli]"

As documented [here](https://meshtastic.org/docs/software/python/cli/).

## Run

On the Pi, to run _meshtastic_ CLI.

    $ meshtastic

Then, to get info about the meshtastic node.

    $ meshtastic --port /dev/ttyACM0 --info

As documented [here](https://meshtastic.org/docs/software/python/cli/#--port-port).
