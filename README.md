# vitotronic_interface

ESP8266 WiFi to serial interface, built to connect to a Viessmann Vitotronic heating control. The heating control is accessed by using an *Optolink*, according to the instructions in the [OpenV wiki](https://github.com/openv/openv/wiki/Adapter-Eigenbau) and made available as TCP/telnet server in the WiFi network. The communication bewteen the ESP8266 and the heating (via the *Optolink*) runs at 4800 bps, 8 bits, even parity, 2 stop bits.

## Flashing the Firmware
1. Install the Arduino IDE plus the 8266 package following the instructions at https://github.com/esp8266/Arduino#installing-with-boards-manager
2. Open, compile and upload the *vitotronic_interface.ino* sketch to your ESP8266.
or
3. Flash the precomiled binary in from the releases directory.

## Setting Up the *Optolink* Hardware
* Wire up the ESP8266 and power supply.
* Connect the *Optolink* to the heating, connect *IN/RX* with the ESP8266 *RX* line and *OUT/TX* with the ESP8266 *TX* line, as well as *VCC* and *GND* to 3.3V and Ground.
* If you want to retrieve debugging output, connnect a serial port's *RX* line to *GPIO2* of the ESP8266 at 115200 bps, 8N1. Don't forget to connect *GND* of the port as well to *GND* of the ESP8266. Works like charm with an FTDI USB-to-serial adapter.

## Configuring the Adapter
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
* Press "Submit" afterwards. The adapter will save the configuration, restart and connect to the given WiFi network. Afterwards the server will be reachable in the network at the IP (DHCP or static) and specified port. The server's IP is also pingable. **Important notice:** Some ESP8266 modules need a "hard reset" to be able to connect to the new WiFi network. Therefore it is recommended to interrupt the power supply for a short time after the new configuration has been submitted. If the connection was successlful, the *vitotronic-interface* network will be gone and the adapter should be pingable in the specified network.

To re-configure the adapter, connect *GPIO12* to *GND* for a short time (e.g. by a pushbutton). Thus, the existing configuration will be deleted and the adapter will enter setup mode again (see above).
