*** Please read as there have been changes to the conf file starting with 0.1.2!!! ***

This utility interfaces ONVIF cameras to Homeassistant. There are webcontrol interface features built in to handle certain aspects of motion or Blue Iris to control recording using the motion detection aspects of the camera itself instead of relying on software motion detection. These webcontrol features may require a bit of work on the part of the end user as I may or may not be using them myself. The current production version I am using will relate to Blue Iris and HomeAssistant as that is my environment. This was originally written for motion/motionEye. This is written to be run in a docker container and the ready to go container I am running can be found with in the registry as myeager1967 / camera-events:latest.

It relies on a configuration file (westfront.conf) that has the following format:

[camera]
name     = westfront
ad110     = False
nightviz = False
user     = camera_username
password = camera_password
address     = 192.168.0.100
port     = 80

[mqtt]
user     = mqtt_username
password = mqtt_password
address     = 192.168.0.10
topic      = BlueIris

[blueiris]
address	 = 192.168.0.4
port = 81
user = name
password = password

If you're going to run this in a native environment instead of Docker, you'll find that some things have to come out and you'll most likely want to hard code the configuration values. I leave this to you but it really isn't very difficult. I'm far from a technical programmer and most of my code is pretty straight forward. Some of the conf file may be out of date but I'll try to keep it current.

The ad110 field is false unless the camera is an AD110 doorbell camera. Might work on the Dahua doorbell as well, seeing as they are basically the same hardware. The doorbell processes motion events slightly different from the rest of the cameras and if you don't set this to True, you'll have WAY more video clips that you'll ever need. The name field under camera isn't used. However, the file must be saved as {cameraname}.conf in a volume you will map in the docker run command. That command is:

docker run --name events-westfront --restart=always --net=host -itd -e CAMERA=westfront -v /etc/localtime:/etc/localtime:ro -v /volume1/docker/camera-events:/config camera-events:latest

You may change the name (I have it as westfront). Whatever you name the .conf file much match the CAMERA= variable. The volume (/volume1/docker/camera-events) will need to match your system. The :/config portion of it gets added to your path.

If you have cameras with IVS events, these events will be detected and published over MQTT as /(topic)/(camera_name)/ivs/(rule_name) and will be on/off. Currently, it only detects line crossing and tripwire. You may name the rules anything you like, but that's how they'll appear in MQTT.
