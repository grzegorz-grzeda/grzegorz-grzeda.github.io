---
layout: post
title: "Simple NTP client in Python"
date: 2023-06-05 07:00:00 -0000
categories: python
---
NTP stands for Network Time Protocol and is the backbone of the Internet. 
Each secure connection needs a timestamp for procise event processing.
If we want to utilise NTP in our own projects (e.g. MicroPython running on ESP32),
we don't need to implement the complete protocol.
We can use the basic information in the NTP frame to get the current timestamp.

For this, we need to build a NTP request frame of 48 bytes. In the first byte we place
the value of `0x1B` (27) and rest must be zeros. I will present the complete meaning of both the
request and response frames another time.

The operating port for both the server and the client is `123`. Transmission goes through UDP.

After receiving the response from server, we parse the data and extract second-to-last `u32` field, 
which is the desired timestamp. The last thing to do is convert the timestamp to a meaningfull value,
becasue the NTP server calculates the timestamp as number of seconds from year 1900.

By substracting the equivalent seconds of `2208988800` to year 1970 (unix epoch) we get the proper value.

To get the `socket.bind()` to work, we need the local IP address of the machine (but not `127.0.0.1`).

# Example

```python
import socket
import struct
from datetime import datetime

NTP_POOL_HOST = 'pool.ntp.org'
MY_IP = '<MY LOCAL NETWORK IP>'

def get_current_timestamp():
    request_list = [27]
    request_list.extend([0] * 47)
    ntp_request = bytes(request_list)

    sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    sock.bind((MY_IP, 123))

    ip = socket.gethostbyname(NTP_POOL_HOST)
    sock.sendto(ntp_request, (ip, 123))
    ntp_response, _ = sock.recvfrom(4096)
    response = struct.unpack('!BBBBIIIIIIIIIII', ntp_response)
    now_timestamp = response[-2] - 2208988800
    return now_timestamp

ts = get_current_timestamp()
print(datetime.fromtimestamp(ts))
```