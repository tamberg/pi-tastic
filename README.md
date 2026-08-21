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
On the Pi, set up a virtual environment _venv_.

    $ cd ~
    $ python3 -m venv venv

Then, activate the virtual environment.

    $ source venv/bin/activate

When done (not now), deactivate it.

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

Expected output, e.g.

    Connected to radio
    
    Owner: Meshtastic 9b60 (9b60)
    My info: { "myNodeNum": 2934283104, ...

As documented [here](https://meshtastic.org/docs/software/python/cli/#--port-port).

## Set the LoRa region
On the Pi, to set the LoRa region, e.g. EU_868.

    $ meshtastic --set lora.region EU_868

Then, to verify the region.

    $ meshtastic --get lora.region

Expected output.
    
    lora.region: 3

As documented [here](https://meshtastic.org/docs/configuration/radio/lora/#region).

## Set a LoRa modem preset
On the Pi, to set a LoRa modem preset, e.g. MEDIUM_FAST.

    $ meshtastic --set lora.modem_preset MEDIUM_FAST

Then, to verify the region.

    $ meshtastic --get lora.modem_preset

Expected output.
    
    lora.modem_preset: 4

As documented [here](https://meshtastic.org/docs/configuration/radio/lora/#modem-preset).

## Create and set a device PSK
On the Pi, to create a _random_ device PSK.

    $ meshtastic --ch-set psk random --ch-index 0

Then, to see the created PSK key.

    $ meshtastic --info | grep "psk=secret"

Expected output, a Base64 encoded PSK.

    ... psk=secret { "psk": "TVlfUEFTU1dPUkQ=", ...

As documented [here](https://meshtastic.org/docs/configuration/radio/channels/#psk-1).

## Set a known device PSK
On a second Pi, to set a Base64 encoded PSK on the primary channel.

    $ meshtastic --ch-set psk "base64:TVlfUEFTU1dPUkQ=" --ch-index 0

As documented [here](https://meshtastic.org/docs/configuration/radio/channels/#psk-1).

## Set a device role
On the Pi, to set meshtastic device role.

    $ meshtastic --set device.role CLIENT

Then, to verify the role.

    $ meshtastic --get device.role

Expected output.
    
    device.role: 0

As documented [here](https://meshtastic.org/docs/configuration/radio/device/#roles)

## List meshtastic nodes
On the sending Pi, to list meshtastic nodes.

    $ meshtastic --nodes

Expected output, e.g.

    ... !aee59b60
    
## Listen for messages
On the receiving Pi, to listen for messages.

    $ meshtastic --listen

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

## Receive a text message
Make sure to [listen for messages](#listen-for-messages) on the receiving Pi.

Then [send a text message](#send-a-text-message) on the sending Pi.

Expected output on the receiving Pi, e.g.

    DEBUG ... Received from radio: packet {
      from: 2934283988
      to: 2934283104
      decoded {
        portnum: TEXT_MESSAGE_APP
        payload: "Hello, you!"
        bitfield: 0
      }
      ...
      transport_mechanism: TRANSPORT_LORA
    }

Note that _2934283104_ in hex is _aee59b60_.

## Connect a sensor
> Note: this part is work in progress

Connect a [supported I2C sensor](https://meshtastic.org/docs/configuration/module/telemetry/) to the [Xiao ESP32S3 pins](https://wiki.seeedstudio.com/xiao_esp32s3_getting_started/#xiao-esp32-s3-front), e.g. a [SHT4x sensor](https://wiki.seeedstudio.com/Grove-SHT4x/).

    - GND
    - 3V3
    - SDA
    - SCL

## Send sensor data
> Note: this part is work in progress

On the sending Pi, to send sensor data to a node.

    $ meshtastic --set device.role SENSOR
    $ meshtastic --ch-set psk random --ch-index 0
    $ meshtastic --set telemetry.environment_update_interval 180
    $ meshtastic --set telemetry.environment_measurement_enabled true

Then, to see the base64 encoded PSK key of the local node.

    $ meshtastic --info | grep "psk=secret"

    ... psk=secret { "psk": "TVlfUEFTU1dPUkQ=", ...

Then, to see log output of the local node.

    $ meshtastic --noproto

Expected output, e.g.

    INFO ... [EnvironmentTelemetry] SHTXX (SHT4x): Got: temp:29.916075degC, hum:44.957504%rh
    INFO ... [EnvironmentTelemetry] SHTXX (SHT4x): Got: temp:29.905394degC, hum:44.970856%rh
    INFO ... [EnvironmentTelemetry] Send: barometric_pressure=0.000000kPa, relative_humidity=44.970856RH, temperature=29.905394degC

## Receive sensor data
> Note: this part is work in progress

On the receiving Pi, to [set PSK](https://meshtastic.org/docs/configuration/radio/channels/#psk-1) on the primary channel.

    $ meshtastic --ch-set psk "base64:TVlfUEFTU1dPUkQ=" --ch-index 0

Also make sure to [listen for messages](#listen-for-messages).

Then [send sensor data](#send-sensor-data) on the sending Pi.

Expected output on the receiving Pi, e.g.

    ...
    environment_metrics {
      temperature: 29.905394
      relative_humidity: 44.970856
    }
