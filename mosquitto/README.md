# MOSQUITTO DOCKER IMAGE

Works on x86 and ARM (Raspberry Pi compatible) Docker Image with mosquitto MQTT broker.
Based upon [fstehle/docker-rpi-mosquitto](https://github.com/fstehle/docker-rpi-mosquitto).

Mosquitto is compiled from skratch in order to add an Auth library

## TODO
- Auth Support

## How to run

```
docker run -it -p 1883:1883 -p 9001:9001 hot/mosqui
```

Exposes Port 1883 (MQTT) 9001 (Websocket MQTT)

Alternatively you can use volumes to make the changes persistent and change the configuration.
```
mkdir -p /srv/mqtt/config/
mkdir -p /srv/mqtt/data/
mkdir -p /srv/mqtt/log/
# place your mosquitto.conf in /srv/mqtt/config/

docker run -it -p 1883:1883 -p 9001:9001 \
-v /srv/mqtt/config:/mqtt/config:ro \
-v /srv/mqtt/log:/mqtt/log \
-v /srv/mqtt/data/:/mqtt/data/ \
--name mqtt hot/mosqui
```
