# vitotronic_interface

ESP8266 WiFi to serial interface, built to connect to a Viessmann Vitotronic heating control. The heating control is accessed by using an *Optolink*, according to the instructions in the [OpenV wiki](https://github.com/openv/openv/wiki/Adapter-Eigenbau) and made available as TCP/telnet server in the WiFi network. The communication bewteen the ESP8266 and the heating (via the *Optolink*) runs at 4800 bps, 8 bits, even parity, 2 stop bits.

## Changelog *Optolink* hardware
### v1.x:
 - ~~GPIO0 for 1-wire~~ don't use as the level shifter is not working
 - GPIO2 for debug messages
 - GPIO12 for config
 - 512k RAM/64k SPIFFS (ESP8266-ESP03/04)
### v2.x:
 - GPIO0 for 1-wire (only from v2.1 onwards)
 - GPIO2 for config (or for debug messages alternatively)
 - 1M RAM/64k SPIFFS (in case of black ESP8266-ESP01 modules)

## Changelog firmware
### v1.0:
Initial version by renemt.

### v1.1 (PeMue):
- added some comments for better understanding
- debug output disabled for default (can be switched on again)

### v1.2 (PeMue):
- added parameter timeout to ensure a proper connection to the router after reboot (e.g. in case of power loss)
- changed default port from 8888 to 81 (like in Lacrosse GateWay)

### v1.3 (PeMue):
- added OTA flash possibility
- print firmware version on setup and success page

### v 2.0 (PeMue):
- added 1-wire temperature measurement (only for hardware >=v2.2)
- check if 1-wire sensors are available and if yes, send the values via UDP
- added the interval for 1-wire measurement to the setup page

## Flashing the firmware
1. Install the Arduino IDE plus the 8266 package following these [instructions](https://github.com/esp8266/Arduino#installing-with-boards-manager)
2. Open, compile and upload the *vitotronic_interface.ino* sketch to your ESP8266

**or**

3. Flash the precomiled binary in from the releases directory.

## Flashing the firmware OTA (**O**ver **T**he **A**ir)
1. Get flash tool from [here](https://1st.bitbumper.de/ota-firmware-update-tool-for-esp8266/) (Windows 64-bit necessary) and run it
2. *Optolink* adapter needs to be configured, that it will be recognized in the local WLAN network
3. Get the IP address of the *Optolink* Adapter, type in IP address and path to new firmware, port remains 8266
4. Press Flash

   ![picture](pic/vitotronic-interface_OTA-pic01.jpg)

5. In case the flashing procedure does not work, disconnect the *Optolink" adapter from power supply, reconnect and try again

**Remark**: If you are flashing a firmware, which has a different layout of the configuration, configuration should be deleted during the first start (see below).

## Setting up the *Optolink* hardware
* Wire up the ESP8266 and power supply.
* Connect the *Optolink* to the heating, connect *IN/RX* with the ESP8266 *RX* line and *OUT/TX* with the ESP8266 *TX* line, as well as *VCC* and *GND* to 3.3V and Ground.
* If you want to retrieve debugging output, connnect a serial port's *RX* line to *GPIO2* of the ESP8266 at 115200 bps, 8N1. Don't forget to connect *GND* of the port as well to *GND* of the ESP8266. Works like charm with an FTDI USB-to-serial adapter.

## Configuring the adapter
As long as the ESP of the adapter is not configured for connecting to a WiFi network it will provide an own WPA2-secured WiFi access point for configuration. To set up the adapter,
* Scan your WiFi environment for the SSID *"vitotronic-interface"*.
* Connect to this network, using the password *"vitotronic"*.
* In your web browser, go to http://192.168.4.1
* Provide the required configuration information:
  * **SSID** of the WiFi network to connect to (mandatory)
  * **Password** for the WiFi network
  * If you want to assign a static IP to the adapter, specify
    * **IP** address to be assigned to the adapter
    * **DNS** server address
    * **Gateway** address
    * **Subnet Mask**
  * The **Port** at which the adapter listens for an incoming connection (mandatory)
  * A **Timeout** value (in s) which the adapter should wait after reboot to re-establish WLAN connection (mandatory).
  * The **1-wire interval** time (in s) when the 1-wire data will be sent (mandatory).

    ![picture](pic/vitotronic_interface_v2.0-pic01.jpg)

* Press "Submit" afterwards. The adapter will save the configuration, restart and connect to the given WiFi network. Afterwards the server will be reachable in the network at the IP (DHCP or static) and specified port. The server's IP is also pingable.
**Important notice:** Some ESP8266 modules need a "hard reset" to be able to connect to the new WiFi network. Therefore it is recommended to interrupt the power supply for a short time after the new configuration has been submitted. If the connection was successful, the *vitotronic-interface* network will be gone and the adapter should be pingable in the specified network.

To re-configure the adapter, connect *GPIO12* (hardware v1.x) (or *GPIO2* (hardware v2.x)) to *GND* for a short time (e.g. by a pushbutton). Thus, the existing configuration will be deleted and the adapter will enter setup mode again (see above).
