## Log 1:

Accumulate various ideas that are viable to the levels of a year 11 project or more. However, the project had to be created within a given deadline. Holding a description that conveys the requirement to successfully complete the project main function.

**Example:**

Mimic robot: Follow made movements from the user for each step, which will loop through the step given.
![](https://lh3.googleusercontent.com/vQN5RUWKrencFo3f2Pjouw17HYzpaDDwxwlCICuR4VJL14CYktL6OIV1HncNukX8YsK8EXmbB1erUeV4TkZYRnPnsXA_1e_ZIWPmawOyN9NKRedlXIzP7kZ__pLvJL7Rf7zAGLjs)

## Log 2:

The next step was to choose one of the following projects collected and further research tutorials and examples that would allow a deeper understanding. The project chosen to continue to the next step being a remote control vehicle. With the various components used for the project to function successfully, and codes needed to be understood beforehand to enable in operating the project with no malfunction. Documenting all of the components in a list of each function and the purpose of using them.

**Example:**

* DC Motor: An electrical machine that is designed to have a rotating point.
  *Utilised to make the vehicle drive around using wheels attached to the DC motor.
* radio.begin()
  * Initialize the modules to begin to communicate.

## Log 3:

With the information documented, we then begin writing codes for the two Arduino microcontroller. One focus as the transmitter and the other acting as the receiver. While assembling the basic components together for the time being to enable both of the microcontrollers to receive and transmit each other. The basic assembly for the two is an Arduino microcontroller connected to nRF24L01+ with wire jumpers.

**Example:**
![](https://lh3.googleusercontent.com/fF9rLrGbamiUOGGto4ndjONKobN3X-onqxSGObLJTUAaIL-5gHGTvPUwHWqW03xStEy1CX7Bz6WMYQ266o1zD1PnA4if5xR0Qdk6yGrGQL1uiOPDbRWx8ojrN9x1uJylXrCFGMlQ)

### Code for Transmitter:
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
  radio.openReadingPipe(0, address);
 
  //Set module as a receiver
  radio.startListening();
}
```
## Log 4:

**Construct a prototype -**

After understanding how all of the codes and components function in an enclosed control environment. We start constructing a crude build of a vehicle and a controller, holding all of the necessary requirement to allow the idea of the project to be visualised. Along with making a decent sketch for the code to be compiled into the microcontroller, allowing the function of driving the vehicle and controlling it with two joysticks. All connected with an nRF24L01+ to enable data from the controller to be sent to the vehicle.

**Draft code sketch:**

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
int key = 2;
int x_key = A0;
int y_key = A2;
int x_pos;
int y_pos;

void setup()
{
  radio.begin();
  Serial.begin(9600);
  
  //set the address
  radio.openWritingPipe(address);
  
  //Set module as transmitter
  radio.stopListening();
  pinMode(key, INPUT);
  digitalWrite(key, HIGH);

  pinMode(x_key, INPUT);
  pinMode(y_key, INPUT);

  
}
void loop()
{
  //Send message to receiver
  const char text[] = "Hello Finn";
  x_pos = analogRead(x_key);
  y_pos = analogRead(y_key);
  int sendvalue[2]={x_pos, y_pos};
  radio.write(&sendvalue, sizeof(sendvalue));
  Serial.print(x_pos);
  Serial.print("   ");
  Serial.println(y_pos);
  delay(100);
}
```

### Receiver -
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
  while (!Serial);
	Serial.begin(9600);
 
  radio.begin();
 
  //set the address
  radio.openReadingPipe(0, address);
 
  //Set module as receiver
  radio.startListening();
  pinMode(3, OUTPUT);
  pinMode(5, OUTPUT);
}
int leftSpeed=0;
int rightSpeed=0;
bool on=false;
bool forward=true;

void loop()
{
  //Read the data if available in buffer
  if (radio.available())
  {
	int values[2]={};
	radio.read(&values, sizeof(values));
	
	Serial.println(values[0]);
	Serial.println(values[1]);
	
	analogWrite(3, values[0]);
	analogWrite(5, values[1]);
  }
 
}
```
## Log 5:

**Construct the final design:**

Which the design both the vehicle and the controller are then further finalised in a compact state. With the controller joysticks embedded into the breadboard with the wires connect to the only to the horizontal and vertical axis. One connected to the horizontal axis and the other one connected to the vertical axis. With the nRF24L01+ separate from the breadboard saves a lot on organising the wire connections.

For the vehicle, as seen in the image below should have the main motor with the wheels on the side front and the last wheel place at the centre-back. Not only that, the microcontroller and the H-bridge are connected to each other, making it much more compact. With the wires much more easily organised to their necessary place. Along with the batteries placed at the back being very accessible to connect the power wires for the microcontroller and the H-bridge.

With the construction with the physical component finish, rework with the code sketch is in need, as it is actually inefficient in its current state. Able to only move forward at a different speed for each of the motors, and it doesn’t have a stationary state.



**Final code Draft:**

Transmitter:
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
Receiver:
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

Comparing it with the final design and the prototype these changes are significant. As the overall design has been modified to be more manageable and compact. To be efficient in its practical.
