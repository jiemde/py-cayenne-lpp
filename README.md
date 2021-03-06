# py-cayenne-lpp

A Python module for the Cayenne Low Power Packet format

It aims to facilate the conversion of values typically read from sensors to a
sequence of bits (the payload) that can be send over a network using the
Cayenne Low Power Packet format. This format is particularly suited for LPWAN
networks such as LoRaWAN.

The payload can then be send for instance to an application of The Things
Network, a LoRaWAN-based community network, which will then forward the data to
a Cayenne application thanks to its Cayenne integration.

The module consists of constants defining the different sensors and their size
and one class CayenneLPP containing the methods to build a payload.

The constants have the format NAME_SENSOR = (LPP id, Data size) where LPP id
is the IPSO id - 3200 and Data size is the number of bytes that must be used
to encode the reading from the sensor.

More info here:
https://mydevices.com/cayenne/docs/lora/#lora-cayenne-low-power-payload-overview

## CayenneLPP class

The class contains the methods to pack data from sensors in a Cayenne LPP
format. The payload structure for the Cayenne LPP format is data frame of
the form: [SENSOR_1, SENSOR_2, ... SENSOR_N], where the format for one
sensor is defined by: [CHANNEL, SENSOR TYPE, DATA].

The channel is an unique identifier for each sensor in the data frame.

The type of sensors compatible with this class are:
- digital input/output;
- analog input/output;
- luminosity (or illuminance) sensor;
- presence sensor;
- temperature sensor;
- humidity sensor;
- accelerometer;
- barometer;
- gyrometer;
- gps.

An object of this class has 3 attributes:
- payload: the data from one or more senors formatted with the Cayenne LPP
           format;
- size: the maximum size of the payload (depends on the network
        on which the data will be send to);
- socket: a socket via which we can send the payload.

The constructor will generate an object with an empty payload and with a
maximum size.

It is possible to reset the payload with the 'reset' method and change the
maximum size with the 'change_size' method.

The current payload and maximum size can be obtained with the methods
'get_payload' and 'get_size' methods.

You can send the payload via the socket using the 'send' method. The socket
can be set using the 'set_socket' method.

To add the data from a sensor, the methods 'add_sensor_name' are provided.

The documentation is provided in the directory `doc`.

### Example

```
# importing the module
import cayenneLPP

# create a LoRa socket
s = socket.socket(socket.AF_LORA, socket.SOCK_RAW)
s.setsockopt(socket.SOL_LORA, socket.SO_DR, 0)
s.setblocking(True)

# creating Cayenne LPP packet
lpp = cayenneLPP.CayenneLPP(size = 100, sock = s)

# adding 2 digital outputs, the first one uses the default channel
lpp.add_digital_input(True)
lpp.add_digital_input(False, channel = 112)

# sending the packet via the socket
lpp.send()
```

Scripts to test the module with a LoPy (https://www.pycom.io/) and The 
Things Network is provided in the `test_lopy` directory. Note that you 
need to update the values of `app_eui` and `app_key` with the correct
credentials.

