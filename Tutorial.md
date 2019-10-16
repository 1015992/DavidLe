# How to make a wireless mini vehicle

## Description:
The idea of making a wireless vehicle is to show the general public how data transmitted from one controller can be utilised in allowing the receiving end to drive the mini vehicle. Understanding the workings of submitting data effectively at a distance. Developing a vehicle that is remotely controlled, holding basic driving mechanics, controlling electronics and motors. The end result would be a joystick controller sending information to a small Lego vehicle to drive forward.

### Hardware Required:
- Arduino x2
- Joystick x2
- DC Motor x2
- Wheels x3
- Batteries (9V)
- Jump wires
- nRF24L01+ 2x (Wireless modules)
- Antenna (attachment) 2x
- H-Bridge
- Breadboards
- Chassis (Vehicle framework)

### Construct:
To begin you need to use the chassis as the basic construct of a vehicle. The structure should be able to hold the arduino, breadboard and the batteries in place. Held together with stickytape in keeping it in place on the vehicle.

### Circuit:
The circuit operates using two nRF24L01+ (wireless modules) acting as a transmitter and receiver. These work by using radio frequency (if the standard 2.4GHz) to achieve transmitting data from one point to another. Which the wires are connected onto the arduino pins, allowing the CSN pin to be set on standby and CE on transmitter when coded. Enabling the modules to communicate on a frequency, on seperate arduino microcontrollers. Allowing to change the property on one module to act as a transmitter or a receiver when coded.  With the wires connected to the breadboard to the arduino to the wireless module. All organised using breadboards.

Focusing on the transmitter side, the two joysticks has two connectors one horizontal (X-axis) and one vertical (Y-axis) which are connected to the arduino analog inputs. Allowing data from the joysticks to be transmitted from the transmitter to the receiver. Using the data received to then inform through the H-bridge to the DC motors the amount of electricity sent.

The DC motors operate in allowing the vehicle to move forward. Connected to the H-bridge to the digital pins, acting as the switch in opening and closing the electricity flows. As the information given from the arduino is from the wireless module connected to the arduino digital pins. Alongside the arduino being powered by a battery capacity to allow the vehicle to operate with its own source of electricity.

### Code:
After constructing the circuits based on the design of the diagram given, we start to code two different sketches. Starting off we set up the necessary libraries needed to utilise the wireless modules in sending data. Each library handles a specific role. Implementing <SPI.h> opens up and handle communication with the SPI. Enabling connection to other outlet using the same module. Along with that another two library <nRF24L01.h> and <RF24.h> which will allow full control of the module.
```
#include <SPI.h>
#include <nRF24L01.h>
#include <RF24.h>
```
More information:
https://lastminuteengineers.com/nrf24l01-arduino-wireless-communication/

Starting with the transmitter, we create global variables. Creating a RF24 object to be able to use the wireless module, requiring two digital pins as it parameter to connect the CSN and CE signal.
```
//create an RF24 object
RF24 radio(9, 8);  // CE, CSN
```
Along with creating a byte array which will serve to act as an address to the two modules to communicate. Requiring to be set up with a 5-byte address enabling to express them in decimal, hex or binary. Which allow you to choose any particular module to communicate.
```
//address through which two modules communicate.
const byte address[6] = "00001";
```
In the setup function, we initialise the radio object using the radio.begin() and we use the radio.openWritingPipe() to set the address of the module.
```
//set the address
radio.openWritingPipe(address);
```
Finally we then use radio.stopListening() function to set the module as a transmitter.
```
//Set module as transmitter
radio.stopListening();
```

In the main loop, the values of the joystick are read by analogRead() function which will be assigned by the given analog pin chosen. Collecting the value being read by the input and storing it to a name box.

In the main loop, we assign a variables with given names for the two joysticks as integers (int). One should focus only on the x-axis and the other one should focus on the y-axis. Which these names will be assigned to a map function, reading the inputs of the analogRead() function with a given analog pin number. With that a range is place with 0 as the lowest input and the 1023 as the highest input possible. Along with the output at the end of the being ranged as -255 as the lowest output and 254 as the highest possible output.
```
int left=map(analogRead(A0), 0, 1023, -255, 254);
int right=map(analogRead(A1), 0, 1023, -255, 254);
```
More information:
https://maker.pro/arduino/projects/how-to-control-a-servo-motor-using-arduino-a-joystick-module-and-nrf24lo1

These variables are then stored into an array with a given name, holding two values given. Which is then sent by using a radio.write() function to the receiver with the data stored. Having the first argument delivering the data needed to be sent and the second argument presenting the size of byte presented using sizeof() function.
```
int power[2]={left,right};
//Send message to receiver
radio.write(&power, sizeof(power));
```
With the Receiver there are similar codes with the transmitter, however, there are some differences. With the addition of fixed integer variables all set to digital pins for the H-bridge.
```
//Include Libraries
#include <SPI.h>
#include <nRF24L01.h>
#include <RF24.h>

//create an RF24 object
RF24 radio(9, 8);  // CE, CSN

//address through which two modules communicate.
const byte address[6] = "00001";

const int channel_a_enable  = 6;
const int channel_a_input_1 = 4;
const int channel_a_input_2 = 7;
const int channel_b_enable  = 5;
const int channel_b_input_3 = 3;
const int channel_b_input_4 = 2;
```
In the setup function we set a radio.openReadingPipe() function to enable a communication with the transmitter set with the same address. Along with using a radio.startListening() function, allowing the module to receive data sent to the specific address. Not only that all of the variables previously assigned are initialized with the mode set to “OUTPUT”. Enabling values to be utilised later and two set the mode later to “HIGH” or “LOW”.
```
//set the address
radio.openReadingPipe(0, address);

//Set module as receiver
radio.startListening();

pinMode( channel_a_enable, OUTPUT );  // Channel A enable
pinMode( channel_a_input_1, OUTPUT ); // Channel A input 1
pinMode( channel_a_input_2, OUTPUT ); // Channel A input 2
  
pinMode( channel_b_enable, OUTPUT );  // Channel B enable
pinMode( channel_b_input_3, OUTPUT ); // Channel B input 3
pinMode( channel_b_input_4, OUTPUT ); // Channel B input 4
```


Before going down to the main loop, two variables are needed to be setup as global variable assigned to zero value. In order to use the data received separately, two different variables are needed to grab one value from the array.
```
int forward=0;
int side=0;
```
In the main loop, an if statement is set to see if data has been received by the transmitter. Acting as a buffer and returning true if any data is available currently.
```
if (radio.available())
```
Alongside setting an assign local variable to be utilised in collecting the data sent after using the radio.read() function to read the data and store it to an array setup before. Of which we then assign the value within the array to premade variables stated before. Splitting it down with one holding the left joystick value and the other holding right joystick value.
```
int text[32] = {0};
radio.read(&text, sizeof(text));
forward=text[0];
side=text[1];
```
Within that is another if statement. That statement would be true when the variable is less than 0, else another if statement takes place seeing if the same variable is greater or equal to 0. When either of them are true a local variable is assigned to the variable, manipulating it to be a float data type. For the first if statement, the variable is multiplied by  negative one to then be divided by a float function set to 255. This float function converts the value into a float data type. For the second if statement, manipulate similar however, the variable is multiplied by a positive while the rest of the calculation is still the same.

________________________________
```
if(forward<0)
    {
      float power=-forward*1/float(255);
      if(side<0)
      {
        float left=(side+255)*1/float(255);
        analogWrite(channel_a_enable, 255*power);
        digitalWrite( channel_a_input_1, LOW);
        digitalWrite( channel_a_input_2, HIGH);

        analogWrite(channel_b_enable, 255*power*left);
        digitalWrite( channel_b_input_3, HIGH);
        digitalWrite( channel_b_input_4, LOW);
      }
      else if(side>=0)
      {
        float left=-(side-255)*1/float(255);
        analogWrite(channel_a_enable, 255*power*left);
        digitalWrite( channel_a_input_1, LOW);
        digitalWrite( channel_a_input_2, HIGH);

        analogWrite(channel_b_enable, 255*power);
        digitalWrite( channel_b_input_3, HIGH);
        digitalWrite( channel_b_input_4, LOW);
      }
    }
    else if(forward>=0)
    {
      float power=forward*1/float(255);
      if(side<0)
      {
        float left=(side+255)*1/float(255);
        analogWrite(channel_a_enable, 255*power);
        digitalWrite( channel_a_input_1, HIGH);
        digitalWrite( channel_a_input_2, LOW);

        analogWrite(channel_b_enable, 255*power*left);
        digitalWrite( channel_b_input_3, LOW);
        digitalWrite( channel_b_input_4, HIGH);        
      }
      else if(side>=0)
      {
        float left=-(side-255)*1/float(255);
        analogWrite(channel_a_enable, 255*power*left);
        digitalWrite( channel_a_input_1, HIGH);
        digitalWrite( channel_a_input_2, LOW);

        analogWrite(channel_b_enable, 255*power);
        digitalWrite( channel_b_input_3, LOW);
        digitalWrite( channel_b_input_4, HIGH);        
      }
    }
  }
```
More information:

https://howtomechatronics.com/tutorials/arduino/arduino-dc-motor-control-tutorial-l298n-pwm-h-bridge/

## Code:
### Transmitter -
```
//Include Libraries
#include <SPI.h>
#include <nRF24L01.h>
#include <RF24.h>

//create an RF24 object
RF24 radio(9, 8);  // CE, CSN

//address through which two modules communicate.
const byte address[6] = "00001";

void setup()
{
  radio.begin();
  
  //set the address
  radio.openWritingPipe(address);
  
  //Set module as transmitter
  radio.stopListening();
}
void loop()
{
  int left=map(analogRead(A0), 0, 1023, -255, 254);
  int right=map(analogRead(A1), 0, 1023, -255, 254);
  int power[2]={left,right};
  //Send message to receiver
  radio.write(&power, sizeof(power));
}
```
### Receiver:
```
//Include Libraries
#include <SPI.h>
#include <nRF24L01.h>
#include <RF24.h>

//create an RF24 object
RF24 radio(9, 8);  // CE, CSN

//address through which two modules communicate.
const byte address[6] = "00001";

const int channel_a_enable  = 6;
const int channel_a_input_1 = 4;
const int channel_a_input_2 = 7;
const int channel_b_enable  = 5;
const int channel_b_input_3 = 3;
const int channel_b_input_4 = 2;

void setup()
{
  while (!Serial);
    Serial.begin(9600);

  pinMode( channel_a_enable, OUTPUT );  // Channel A enable
  pinMode( channel_a_input_1, OUTPUT ); // Channel A input 1
  pinMode( channel_a_input_2, OUTPUT ); // Channel A input 2
  
  pinMode( channel_b_enable, OUTPUT );  // Channel B enable
  pinMode( channel_b_input_3, OUTPUT ); // Channel B input 3
  pinMode( channel_b_input_4, OUTPUT ); // Channel B input 4
  
  radio.begin();
  
  //set the address
  radio.openReadingPipe(0, address);
  
  //Set module as receiver
  radio.startListening();
}
int forward=0;
int side=0;
void loop()
{
  //Read the data if available in buffer
  if (radio.available())
  {
    int text[32] = {0};
    radio.read(&text, sizeof(text));
    //Serial.println(text[0]);
    //Serial.println(text[1]);
    forward=text[0];
    side=text[1];

    if(forward<0)
    {
      float power=-forward*1/float(255);
      if(side<0)
      {
        float left=(side+255)*1/float(255);
        analogWrite(channel_a_enable, 255*power);
        digitalWrite( channel_a_input_1, LOW);
        digitalWrite( channel_a_input_2, HIGH);

        analogWrite(channel_b_enable, 255*power*left);
        digitalWrite( channel_b_input_3, HIGH);
        digitalWrite( channel_b_input_4, LOW);
      }
      else if(side>=0)
      {
        float left=-(side-255)*1/float(255);
        analogWrite(channel_a_enable, 255*power*left);
        digitalWrite( channel_a_input_1, LOW);
        digitalWrite( channel_a_input_2, HIGH);

        analogWrite(channel_b_enable, 255*power);
        digitalWrite( channel_b_input_3, HIGH);
        digitalWrite( channel_b_input_4, LOW);
      }
    }
    else if(forward>=0)
    {
      float power=forward*1/float(255);
      if(side<0)
      {
        float left=(side+255)*1/float(255);
        analogWrite(channel_a_enable, 255*power);
        digitalWrite( channel_a_input_1, HIGH);
        digitalWrite( channel_a_input_2, LOW);

        analogWrite(channel_b_enable, 255*power*left);
        digitalWrite( channel_b_input_3, LOW);
        digitalWrite( channel_b_input_4, HIGH);        
      }
      else if(side>=0)
      {
        float left=-(side-255)*1/float(255);
        analogWrite(channel_a_enable, 255*power*left);
        digitalWrite( channel_a_input_1, HIGH);
        digitalWrite( channel_a_input_2, LOW);

        analogWrite(channel_b_enable, 255*power);
        digitalWrite( channel_b_input_3, LOW);
        digitalWrite( channel_b_input_4, HIGH);        
      }
    }
  }
}
```
Extra Information:

https://www.tutorialspoint.com/arduino/arduino_dc_motor.htm

https://dronebotworkshop.com/dc-motors-l298n-h-bridge/#H-Bridge

https://arduino.stackexchange.com/questions/1612/is-it-possible-to-transmit-using-an-nrf24l01-without-an-arduino

https://howtomechatronics.com/tutorials/arduino/arduino-wireless-communication-nrf24l01-tutorial/
