
# 🏠 SmartHaus
[![Build Status](https://travis-ci.org/amiroff157/smarthaus.svg?branch=master)](https://travis-ci.org/amiroff157/smarthaus)
[![GPLv3 license](https://img.shields.io/badge/License-GPLv3-blue.svg)](http://perso.crans.org/besson/LICENSE.html)

SmartHaus is a home automation system for building real-time dashboards for monitoring and controlling IoT devices. It works with microcontrollers like the Arduino Uno/Mega/Duo and the ESP8266 or ESP32.
Built with Angular on the client-side and Node.js on the server-side and communicates via a RESTful API.

![](https://miro.medium.com/fit/c/1400/420/1*ekrneFlaBAAqotbwcJ0pDA.png)
#### Superpowers:
1. ⚡ ****Robust****: Full JavaScript stack with Angular and Node.js with MongoDB database (MEAN Stack)
3. 👌 ****Convenient****: Operates from your local network where your IoT devices live
4. 🔐 ****Secure****: It's centralized in your network and does not require an external "cloud" server
5. 👍 ****Simple****: No need for external MQTT broker configurations. It's fully RESTful!

## Installation
* On your device, upload one of the required Arduino example sketches that are available in the [sketches](https://github.com/amiroff157/smarthaus/tree/master/embedded/sketches) directory of this project. Please refer to the [Embeded API](https://github.com/amiroff157/smarthaus#embedded-api) in the docs section.
* Set up the system on a Raspberry Pi. Please refer to [this](https://medium.com/@amiroff157/the-most-robust-and-secure-home-automation-system-6d0ddbb39f29) tutorial.

⚠️ User signup is initially protected both on the client and backend. Edit the following files to allow access:
1. [backend/api/index.js](backend/api/index.js#L140) - remove `userCtrl.verifyJWT`
2. [src/app/app-routing.module.ts](src/app/app-routing.module.ts#L12)  - remove `canActivate: [AuthGuard]`


## Client API
The API is similar to the official [Arduino](https://www.arduino.cc/reference/en/#functions) with Analog & Digital I/O functions except for the ```js callFunction()``` and the ```getVariable()``` functions.


### pinMode()
```js
pinMode(id, pin, mode)
```
Configures the specified pin to behave either as an input or an output
#### Parameters:
**id:** device ID.
**pin:** the pin number.
**mode:** 'i' for INPUT, 'o' for OUTPUT and 'I' for INPUT_PULLUP.
#### Example:
```js
// Sets pin 6 mode to OUTPUT.
pinMode('468792', 6, 'o')
```
⚠️ https://www.baldengineer.com/when-to-use-arduinos-pinmode-and-why.html
___
### digitalWrite()
```js
digitalWrite(id, pin, value)
```
Writes a HIGH or a LOW value to a digital pin
#### Parameters:
**id:** device ID.
**pin:** the pin number to write to.
**value:** 1 for 'HIGH' and 0 for 'LOW'.
#### Example:
```js
// Sets pin 5 state to HIGH.
digitalWrite('468792', 5, 1)
```

___
### digitalRead()
```js
digitalRead(id, pin)
```
Reads the state value from a specified digital pin
#### Parameters:
**id:** device ID.
**pin:** the pin number to read from.
#### Example:
```js
// Reads pin 4 state value and prints it to the console
digitalRead('468792', 4)
    .subscribe((data) => {
        console.log(data);
    });
```

___
### analogWrite()
```js
analogWrite(id, pin, value)
```
Writes an analog value (PWM wave) to a pin
#### Parameters:
**id:** device ID.
**pin:** the pin to write to.
**value:** the duty cycle: between 0 (always off) and 255 (always on).
#### Example:
```js
// Writes an analog value to pin 3
analogWrite('468792', 3)
```

___
### analogRead()
```js
analogRead(id, pin)
```
Reads the value from the specified analog pin
#### Parameters:
**id:** device ID.
**pin:** the pin to read from.
#### Example:
```js
// Reads the voltage from pin 2 and prints it to the console
analogRead('468792', 2)
    .subscribe((data) => {
        console.log(data);
    });
```

___
### getVariable()
```js
getVariable(id, variable)
```
Reads a variable from the device. This is useful for reading sensor data
#### Parameters:
**id:** device ID.
**variable:** variable name to read from.
#### Example:
```js
// Gets the value of variable 'temperatre' and prints it to the console
getVariable('468792', 'temperature')
    .subscribe((data) => {
        console.log(data);
    });
```

___
### callFunction()
```js
callFunction(id, called_function, parameters)
```
Executes a pre-defined function on the device
#### Parameters:
**id:** device ID.
**called_function:** function name to call
**parameters:** parameters to pass to the function
#### Example:
```js
// Calls the function 'turn_on_led' and passes the value 1
callFunction('468792', 'turn_on_led', '1')
```
___
## Embedded API
The corresponding Arduino library "Restfulino" was forked from the [aREST](https://github.com/marcoschwartz/aREST) library.
The changes made in this version were necessary in order to disconnect the library from the centralized private aREST MQTT broker since this system doesn't rely on MQTT. Other minor tweaks were also applied.
The library is published under the same licence of aREST.

##### Initializes a variable and exposes it to RESTful API
```c
int temperature;
rest.variable("variable name", &temperature);
```

##### Sets a name for the device
```js
rest.set_name("Weather Station");
```

##### Sets an ID for the device
```js
rest.set_id("841239");
```
The ID should be 6 characters long (will be automatically generated if not set)

##### Handle the client in the ```loop()``` function.
```js
rest.handle(client);
```

After uploading the sketch using the Arduino IDE, get the IP address from the serial monitor, enter it in your browser, and you should receive something like this:
```json
{"variables": {}, "id": "468792", "name": "Weather Station", "hardware": "esp8266", "connected": true}
```
It should return a JSON body with all the device data.
___
## 📑To-Do List
- [x] Add Raspberry Pi control support
- [x] Add SSL Certificate on Node.js server
- [ ] Enable TLS on MongoDB server
- [x] Add MongoDB database
- [x] Add JWT authentication for client and server HTTP requests
- [ ] Add dynamic dashboard



## License
SmartHaus is licensesd under the [GPL-3.0](https://github.com/amiroff157/smarthaus/blob/master/LICENSE) license.
