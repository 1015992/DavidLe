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
  int left=map(analogRead(A0), 0, 1023, 0, 511);
  int right=map(analogRead(A1), 0, 1023, 0, 511);
  int power[2]={left,right};
  //Send message to receiver
  radio.write(&power, sizeof(power));

}
```
