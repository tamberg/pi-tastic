# Pi-tastic
Connect a Pi with [Meshtastic](https://meshtastic.org).

Initiated by Michel of [Lug Limbe](https://sokolo.cronopios.org/) ([Map](https://www.openstreetmap.org/search?query=limbe%20linux#map=19/4.01908/9.17187)).

## Overview
- A Pi computer sends messages to another Pi.
- They communicate over a LoRa mesh network.
- We use Xiao MCUs with LoRa radio modules.

Here is the final setup.

                       Sender                  Mesh Network                   Receiver
    
    [Pi Computer]-USB Serial-[Xiao MCU w/ LoRa]-----...---->[Xiao MCU w/ LoRa]-USB Serial-[Pi Computer]

## Pi computer
- Pi computer w/ Linux
- Headless install is fine
- We use a terminal via SSH

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

On the Pi, set up a venv.

    $ python -m venv venv

Then, activate the venv.

    $ source venv/bin/activate

When done (not now).

    $ deactivate
