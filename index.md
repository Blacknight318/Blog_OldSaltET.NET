# WIP is bad, we must reduce the WIP!!

Let's try a code quote, this one is from my sensor using an Adafruit Trinket M0 and and DHT22, This will wait for a poll command and try to pull the data from the DHT22, if not it'll loop around.
 
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
