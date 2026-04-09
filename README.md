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
- Use a terminal via SSH.

On your computer, to log into the Pi.

    $ ssh pi@raspberrypi.local

## Xiao MCU w/ LoRa
- [Seeed Xiao ESP32S3 MCU](https://wiki.seeedstudio.com/wio_sx1262_with_xiao_esp32s3_kit/).
- Wio SX1262 LoRa Module.
- Connected to Pi via USB.

On the Pi, to list USB devices, e.g. _/dev/ttyACM0_.

    $ ls -al /dev/ttyA*
    ...
    crw-rw---- 1 root dialout ... /dev/ttyACM0

## Install MCU firmware (optional)
> Note: Xiao MCUs have firmware pre-flashed.

If you have to (re)flash the Xiao MCU.

- Use Chrome (other browsers don't work).
- Open https://flasher.meshtastic.org/

## Set up a venv for Python
On the Pi, set up a _venv_.

    $ python -m venv venv

Then, activate the _venv_.

    $ source venv/bin/activate

When done (not now).

    $ deactivate

As documented [here](https://packaging.python.org/en/latest/guides/installing-using-pip-and-virtual-environments/#create-and-use-virtual-environments).

## Install meshtastic CLI
On the Pi, install _meshtastic_ CLI.

    $ pip3 install --upgrade "meshtastic[cli]"

As documented [here](https://meshtastic.org/docs/software/python/cli/).

## Run meshtastic CLI
On the Pi, to run _meshtastic_ CLI.

    $ meshtastic

Then, to get info about the meshtastic node.

    $ meshtastic --port /dev/ttyACM0 --info

As documented [here](https://meshtastic.org/docs/software/python/cli/#--port-port).

## Set meshtastic LoRa region
On the Pi, to set _meshtastic_ LoRa region, e.g. EU_868.

    $ meshtastic --set lora.region EU_868

Then, to verify the region.

    $ meshtastic --get lora.region

Expected output.
    
    lora.region: 3

As documented [here](https://meshtastic.org/docs/configuration/radio/lora/#region).

## Set meshtastic device role
On the Pi, to set _meshtastic_ device role.

    $ meshtastic --set device.role CLIENT

Then, to verify the role.

    $ meshtastic --get device.role

Expected output.
    
    device.role: 0

As documented [here](https://meshtastic.org/docs/configuration/radio/device/#roles)

## Listen for messages
On the receiving Pi, to listen for messages.

    $ meshtastic --listen

## List meshtastic nodes
On the sending Pi, to list _meshtastic_ nodes.

    $ meshtastic --nodes
    ... !aee59b60
    
## Send a text message
On the sending Pi, to send a message to all nodes.

    $ meshtastic --sendtext 'Hello, all!'
    Sending text message Hello, all! to ^all on channelIndex:0

Or, to send a message to a specific node, e.g. _!aee59b60_.

    $ meshtastic --dest '!aee59b60' --sendtext 'Hello, you!'
    Sending text message Hello, you! to !aee59b60 on channelIndex:0 

Or, to send a message on a specific channel, e.g. _1_.

    $ meshtastic --ch-index 1 --sendtext 'Hello, channel!'
    Sending text message Hello, channel! to ^all on channelIndex:1
