When creating the prototype towards the project, research is a necessary tool in finding inspiration.

How to Control a Servo Motor Using Arduino UNO, a Joystick Module, and NRF24L01 Modules
* Move the joystick in any direction sends an analog value to the Arduino.
* Setting the NRF24L01 module in the transmitter mode will send the joystick movement value at a specific address.
* Setting the NRF24L01 module in the receiving mode will receive data sent.
  * Which the Arduino will read and process it.
* NRF24L01 module consumes less power than a Led ( usage of 12mA power during transmission).
  * Operates only at 3.3v, connecting to a 5v will damage it.
  * The other pins acceptance to connect to 5v.
* The pins SCK, MOSI, and MISO are for SPI communication.
* The pin CSN is the setting for active mode or standby.
* The pin CE is the setting for command mode or transmit

How nRF24L01+ Wireless Module Works & Interface with Arduino

Hardware Overview:
* Design to operate a radio frequency in 2.4 GHz ISM frequency. Plus uses GFSK modulation for data transmission.
  * Data transfer rate can be 250kbps, 1Mbps and 2Mbps.
* Module communicates using Serial Peripheral Interface (SPI), which parameters of the frequency channel, power output and data rate can be configured through SPI.
* The SPI bus uses the concept of master and slave. Arduino master and module slave.
* Functioning by having the modules on the same frequency aka Channel.
* The nRF24L01+ also features a multiceiver, allowing multiple transmitters send information to one a receiver acting as a hub.

## References
HowToMechatronics. (2019). Arduino DC Motor Control Tutorial - L298N | PWM | H-Bridge - HowToMechatronics. [online] Available at: https://howtomechatronics.com/tutorials/arduino/arduino-dc-motor-control-tutorial-l298n-pwm-h-bridge/ [Accessed 4 Oct. 2019].

Last Minute Engineers. (2019). In-Depth: How nRF24L01 Wireless Module Works & Interface with Arduino. [online] Available at: https://lastminuteengineers.com/nrf24l01-arduino-wireless-communication/ [Accessed 4 Oct. 2019].

Maker Pro. (2019). How to Control a Servo Motor Using Arduino UNO, a Joystick Module, and NRF24L01 Modules | Arduino. [online] Available at: https://maker.pro/arduino/projects/how-to-control-a-servo-motor-using-arduino-a-joystick-module-and-nrf24lo1 [Accessed 4 Oct. 2019].
