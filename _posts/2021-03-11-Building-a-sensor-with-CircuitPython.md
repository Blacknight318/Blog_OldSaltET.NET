## Why build this?
This project started as a way to monitor my home air and see just how much the temperature and humidity fluctuated during the day, and how well the Nest thermostat maintained everything.  So after connecting it to a feed on [Adafruit IO](https://io.adafruit.com) and creating a dashboard it was ready to go!

## The Parts
1. [Trinket M0](https://www.amazon.com/Adafruit-Trinket-M0-CircuitPython-Arduino/dp/B07MLWKK6V)
2. [DHT22](https://www.amazon.com/dp/B07XBVR532/ref=cm_sw_r_em_apa_fabc_NRTX4BSZW5RQVF3P930T?_encoding=UTF8&psc=1)
    * Note the above DHT22 has an adapter board, if you use a regular DHT22 you will need a pull up resistor(4.7k or 10k) between data and VCC and a filter capitor(0.1uF or 0.01uF) between data and ground.
3. Micro USB cable for Trinket M0 to connect to computer or Raspberry Pi
4. Jumper wires
5. Breadboard (optional)

## The Sensor Code
First lets talk about the code for the sensor itself.  You'll want to get the latest update for CircuitPython for the Trinket M0 [here](https://circuitpython.org/board/trinket_m0/) and the latest libraries [here](https://circuitpython.org/libraries). Once you have those updated you'll want to copy the adafruit_dotstar.mpy and adafruit_dht.mpy files to the lib folder on the Trinket M0.  Then copy the code below to the main.py file on the root of the Trinket M0.

```python
import board
import adafruit_dht
import time

dht = adafruit_dht.DHT22(board.D2)

while True:
    command = input()
    if command == "poll":
        time.sleep(1)
        retry = True
        while retry:
            try:
                dht.measure()
                tempC = dht.temperature
                tempF = (tempC * (9/5))+32
                print('{},{}'.format(tempF, dht.humidity))
                retry = False
            except:
                pass
    else:
        pass
```
Now that we have the code copied you can open a serial connection with something like Mu Editor and send the "poll" command, this should return the temp(in degrees Farenheit) and the humidity percentage. _Note the 1 second delay, this is to allow closing the send port and opening for receive on the computer side._

## Some sample code to read the sensor with Python
```python
import time
import serial # using pyserial for this instance

com = 'COM3' # On linux will usually be something like /dev/ttyACM0

def pull_data():
    try:
        port = serial.Serial(com, 9600)
        port.write(b'poll\r\n')
        port.close()
        port.open()
        reading = port.readline().decode().strip('\r\n')
        port.close()
        return reading
    except serial.serialutil.SerialException:
        print("Failed to connect over USB {} at {}".format(com, time.ctime()))
        return None
```

## Where to from here?
There's still more yet to do with this project, create a class to read out the sensor and put up the code used to send data to the feed/dashboard on [Adafruit IO](https://io.adafruit.com). we'll also be working on a single board the encompasses the sensor and a version of the ATSAMD21 that is compatible with the above mentioned [CirctuiPython](https://circuitpython.org/) libraries, and hopefully a 3D printer case as well.  This comes after helping a coworker use this same setup with their Raspberry PI to monitor their home network cabinet.  Stay tuned for more updates!