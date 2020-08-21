# message formats

BGP send messages over TCP.

The smallest BGP message is only the BGP header which is 19 Bytes (octets).  Commonly used for keep-alive messages.

The BGP message header is fixed-size, it consists of

* 16Byte Marker (all 0xFF)
* 2Byte Length (including hre BGP header (19Bytes to maximum of 4096Bytes)
* 1Byte Type


There are 4 type codes:

* Open (Type 1)
* Update (2)
* Notification
* Keepalive


All types except the **keepalive** extend the fixed-size header with additional message fields.

### OPEN



### UPDATE



### NOTIFICATION



### KEEPALIVE




