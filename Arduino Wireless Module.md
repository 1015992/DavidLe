# How Wireless Modules Function

## Outline:
Examining how the component operates in further depth.

## Component:
nRF24L01+ is a transceiver module that transfer data, allowing comunicatoin between similar trandceiver module. As seen below of the image of the module with the addition of an antenna attachment.

![](https://lastminuteengineers.com/wp-content/uploads/2018/07/nRF24L01-PA-LNA-External-Antenna-Wireless-Transceiver-Module.png)

## Overview:
The design of the nRF24L01+ enables transmission of data through radio frequency. Which the radio frequency operates in 2.4 GHz ISM frequency band along with using a GFSK moduleation to transfer data.

The 2.4 GHz ISM band is reseved for the use of unlisensed low-powered devices. As this 2.4 GHz band is one of the Industrial, Scientific, and Medical (ISM) bands internatilly resever for these type of devices. Example being cordless phone or bluetooth devices.

Along with the module consumption of power when in operation, mainly uses 1.9V - 3.6V. However, the logic pins on the module will allow it to use 5V.

Not only that, the nRF24L01+ module communicate through a 4-pin Serial Peripheral Interface which transfer data at a maximun rate of 10mbps. With parameter being radio frequency, the output power and the data rate able to be modified when using the SPI interface.

## nRF24L01+ Transceiver Module Pinout:
Examining the description of each pinout of the module. As seen below.
* GND - The Ground Pin
* VCC - supplies power to the modules, but only allowing volatage between 1.9V to 3.6V. As ustilising 5 volts will damage the module at a high probability.
* CE (Chip Enable) - An active-HIGH pin. This will allow the modules to transmit or receive, depending on the mode is selected.
* CSN - An active-LOW pin and normally kept HIGH. Allowing the nRF24L01+ to begin listening on its SPI port data and process it when set to Low.
* SCK - Accepts clock pulses provided by the SPI bus master.
* MOSI - The SPI input.
* MISO - The SPI output
* IRQ - An interuption pin which alerts when new data is available to process to the master.

The diagram below displays the location of each pin.

![](https://lastminuteengineers.com/wp-content/uploads/2018/07/Pinout-nRF24L01-PA-LNA-External-Antenna-Wireless-Transceiver-Module.png)

The image below diagrams where the pins should to setup with the Arduino. But in order to allow to transmit and receive two with circuits are to be made.

![](https://lastminuteengineers.com/wp-content/uploads/2018/07/Arduino-Wiring-Fritzing-Connections-with-nRF24L01-PA-LNA-External-Antenna-Wireless-Module.png)

The way the modules transfer information to one nRF24L01+ to another is shown below of how it operates. Transmitting the data in a package to the receiver, which is then holds an acknowlegdement package. When the receiver gets the package sends the acknowlegemtn package to the transmitter. Which will then activate the interrupt signal to indicate new data is available.

![](https://lastminuteengineers.com/wp-content/uploads/2018/07/nRF24L01-Transceiver-Working-Packet-Transmission.gif)


Reference: https://lastminuteengineers.com/nrf24l01-arduino-wireless-communication/
