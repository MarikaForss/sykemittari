# sykemittari

Arduino koodi:

```

#define USE_ARDUINO_INTERRUPTS true    
#include <PulseSensorPlayground.h>        


const int PulseWire = 0;      
const int LED13 = 13;          
int Threshold = 550;           
                               
                                
                               
PulseSensorPlayground pulseSensor;  


void setup() {   

  Serial.begin(9600);         

   
  pulseSensor.analogInput(PulseWire);   
  pulseSensor.blinkOnPulse(LED13);       
  pulseSensor.setThreshold(Threshold);   

  
   if (pulseSensor.begin()) {
    //Serial.println("We created a pulseSensor Object !");   
  }
}



void loop() {

 int myBPM = pulseSensor.getBeatsPerMinute();  
                                               

if (pulseSensor.sawStartOfBeat()) {            
 //Serial.println("♥  A HeartBeat Happened ! "); 
 //Serial.print("BPM: ");                        
 Serial.println(myBPM);                        
}

  delay(20);                    

}

```
Python koodi:
```

import serial
import time
import mariadb
from datetime import datetime

device ='/dev/ttyACM0'
try:
    print("availee arduinoa", device)
    arduino = serial.Serial(device, 9600)
   
except:
    print ("arduinoa ei saatu kiinni: ", device)
    
   
conn = mariadb.connect(user="root", password="xxx", host="localhost",database="Sykemittari")
cur=conn.cursor()

while True:
    try:
        time.sleep(5)
        data = arduino.readline()
        print(data.decode("utf-8"))
        BPM = float(data.decode("utf-8"))
        sql = f"INSERT INTO Sykkeet(pvm,bpm) VALUES (now(),{BPM})"
        val = (BPM)
        cur.execute(sql) 
        conn.commit()  
        
    except:
        print ("looppi ei toimi")
        
conn.close()

```
Mariadb:hen loin Tietokannan; Sykemittari mihin loin taulukon Sykkeet. 
```
| Sykkeet | CREATE TABLE `Sykkeet` (
  `pvm` datetime DEFAULT NULL,
  `bpm` float DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 |

```
