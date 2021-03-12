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

## Sample code to read the sensor for Python
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