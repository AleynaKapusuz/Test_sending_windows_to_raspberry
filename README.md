# Test Sending Messages from Windows to Raspberry Pi via MQTT

## 1- Install "mosquitto" on Raspberry Pi

```
sudo apt update
sudo apt upgrade -y
sudo apt install -y mosquitto mosquitto-clients
sudo systemctl enable mosquitto
sudo systemctl start mosquitto
```
## 2- Check if it's running
```
sudo systemctl status mosquitto
```

The output shoulde be like:
```
Active: active (running)
```

## 3- Check if the port is being listened to :

```
sudo netstat -tulnp | grep 1883
```

## 4- Configure Mosquitto to Acept Remote Connections :
```
sudo nano /etc/mosquitto/mosquitto.conf
```

Add the following lines at the end of the file :  

```
listener 1883
allow_anonymous true
```

The run :
```
sudo systemctl restart mosquitto
sudo netstat -tulnp | grep 1883
```

EXPECTED OUTPUT :
```
tcp 0 0 0.0.0.0:1883 0.0.0.0:* LISTEN 5896/mosquitto
```

## 5- Code to Run on windows :
```
import paho.mqtt.client as mqtt
import time

mqtt_client = mqtt.Client()
mqtt_client.connect("192.168.<RASPBERRY_IP>", 1883)
mqtt_client.loop_start()

try:
    while True:
        mqtt_client.publish("test", "hello raspberry")
        print("Message sent.")
        time.sleep(1)  # wait 1 second
except KeyboardInterrupt:
    print("Sending stopped.")
    mqtt_client.loop_stop()
    mqtt_client.disconnect()
```

## 6- Code on Run to Raspberry Pi :
```
mosquitto_sub -t test
```
