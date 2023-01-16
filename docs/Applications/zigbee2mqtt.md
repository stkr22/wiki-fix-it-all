# Zigbee2MQTT

## Tuya switches consumed energy reset

To reset "Sum of consumed energy", use the Dev console and execute: Endpoint: 1 Cluster: 0x00 Command: 0 Payload: (don't change this)

Sources:

- <https://www.zigbee2mqtt.io/devices/TS011F_plug_3.html#reset-energy>

## Issues with conbeeii

```bash
docker run -it --rm --entrypoint "/firmware-update.sh" --privileged --cap-add=ALL -v /dev:/dev -v /lib/modules:/lib/modules -v /sys:/sys deconzcommunity/deconz

```

Sources:

- <https://github.com/Koenkk/zigbee2mqtt/issues/9554>
- <https://www.zigbee2mqtt.io/guide/adapters/#recommended>
- <https://github.com/deconz-community/deconz-docker#updating-conbeeraspbee-firmware>
- <https://github.com/dresden-elektronik/deconz-rest-plugin/wiki/Update-deCONZ-manually>
- <https://github.com/deconz-community/deconz-docker#readme>
