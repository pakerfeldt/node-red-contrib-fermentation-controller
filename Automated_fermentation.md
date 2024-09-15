# Automated fermentation

This guide will walk you through how to accomplish a fully automated multi-step fermentation using this Node-RED library along with a few other. 
It is assumed that you are already familiar with Node-RED, if not I suggest you read up on that before.

We will be using the following Node-RED libraries:
* [Temperature Controller](https://flows.nodered.org/node/@pakerfeldt/node-red-contrib-temperature-controller) - Our software thermostat. For those of you familiar with STC-1000 or Inkbird, this is our software version of that. You will need hardware as well, more about that in a bit!
* [Adaptive Temperature](https://flows.nodered.org/node/@pakerfeldt/node-red-contrib-adaptive-temperature) - Takes desired target temperature, and two temperature readings and produces a smoothened temperature.
* [Fermentation Controller](https://flows.nodered.org/node/@pakerfeldt/node-red-contrib-fermentation-controller) - This node is responsible for executing your fermentation schedule.
* [Shelly](https://flows.nodered.org/node/node-red-contrib-shelly) - Node for communicating with Shelly devices.

# Hardware
We are going to use a Shelly Plus 2PM + Shelly Plus Addon + 2 Shelly Temperature Sensors. The sensors are connected to the Shelly Plus Addon which in turn is attached to the Shelly Plus 2PM.
The 2PM has two channels, where one is used for cooling and one for heating. Connect each channel to an output socket. Always consult with electrician!
For gravity readings, we are going to use an iSpindel that communicates over MQTT which Node-RED has built-in support for. 

One sensor probe will be positioned somewhere inside the refrigerator to measure ambient temperature. The other probe should be positioned for reading the temperature in your fermentation
vessel, through for example a thermowell. If this is not possible, you can use the temperature readings from iSpindel or any other way you see fit.

# Hooking up Shelly

​Add the Shelly devices to your network by using their mobile app, but do not add them as a "Thermostat" since we will be using Temperature Controller in Node-RED as our thermostat.
Each of the two channels will become its own device in Shelly and you add one temperature sensor to each of these two devices.

# Node-RED

Install the four libraries to your Node-RED instance.

