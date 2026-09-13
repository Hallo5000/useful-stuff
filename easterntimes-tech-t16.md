## Reverse engineering of the _Easterntimes Tech T16_ Gaming Mouse

Information gathered by `lsusb -v`:
```
ID 258a:1007 SINOWEALTH Game Mouse
Couldn't open device, some information will be missing
Device Descriptor:
  bLength                18
  bDescriptorType         1
  bcdUSB               1.10
  bDeviceClass            0 [unknown]
  bDeviceSubClass         0 [unknown]
  bDeviceProtocol         0 
  bMaxPacketSize0         8
  idVendor           0x258a SINOWEALTH
  idProduct          0x1007 Game Mouse
  bcdDevice            1.00
  iManufacturer           1 SINOWEALTH
  iProduct                2 Game Mouse
  iSerial                 0 
  bNumConfigurations      1
  Configuration Descriptor:
    bLength                 9
    bDescriptorType         2
    wTotalLength       0x003b
    bNumInterfaces          2
    bConfigurationValue     1
    iConfiguration          0 
    bmAttributes         0xa0
      (Bus Powered)
      Remote Wakeup
    MaxPower              480mA
    Interface Descriptor:
      bLength                 9
      bDescriptorType         4
      bInterfaceNumber        0
      bAlternateSetting       0
      bNumEndpoints           1
      bInterfaceClass         3 Human Interface Device
      bInterfaceSubClass      1 Boot Interface Subclass
      bInterfaceProtocol      2 Mouse
      iInterface              0 
        HID Device Descriptor:
          bLength                 9
          bDescriptorType        33
          bcdHID               1.11
          bCountryCode            0 Not supported
          bNumDescriptors         1
          bDescriptorType        34 Report
          wDescriptorLength      71
          Report Descriptors: 
            ** UNAVAILABLE **
      Endpoint Descriptor:
        bLength                 7
        bDescriptorType         5
        bEndpointAddress     0x81  EP 1 IN
        bmAttributes            3
          Transfer Type            Interrupt
          Synch Type               None
          Usage Type               Data
        wMaxPacketSize     0x0008  1x 8 bytes
        bInterval               1
    Interface Descriptor:
      bLength                 9
      bDescriptorType         4
      bInterfaceNumber        1
      bAlternateSetting       0
      bNumEndpoints           1
      bInterfaceClass         3 Human Interface Device
      bInterfaceSubClass      1 Boot Interface Subclass
      bInterfaceProtocol      1 Keyboard
      iInterface              0 
        HID Device Descriptor:
          bLength                 9
          bDescriptorType        33
          bcdHID               1.11
          bCountryCode            0 Not supported
          bNumDescriptors         1
          bDescriptorType        34 Report
          wDescriptorLength     259
          Report Descriptors: 
            ** UNAVAILABLE **
      Endpoint Descriptor:
        bLength                 7
        bDescriptorType         5
        bEndpointAddress     0x82  EP 2 IN
        bmAttributes            3
          Transfer Type            Interrupt
          Synch Type               None
          Usage Type               Data
        wMaxPacketSize     0x0008  1x 8 bytes
        bInterval               1
```

| Interface	| Class | Subclass	| Protocol	| Endpoint	| Report descriptor |
| --- | --- | --- | --- | --- | --- |
| 0	| HID	| Boot	| Mouse	| 0x81 IN	| 71 bytes |
| 1 | HID | Boot | Keyboard | 0x82 IN	| 259 bytes |

### 0x81 Endpoint (HID; `bInterfaceProtocol = Mouse`)
is used for standard mouse buttons (Left/Right/Middle -Click, Mouse Wheel) and movement:
(In Wireshark the actual payload is 7 bytes in the 'Leftover Capture Data')

**Payload**
| Bytes | Functionality |
| --- | --- |
| Byte 0 | Buttons |
| Bytes 1-2 | X movement, signed 16-bit little-endian |
| Bytes 3-4 | Y movement, signed 16-bit little-endian |
| Byte 5 |	Maybe: Scroll wheel,	Signed 8-bit relative value |
| Byte 6 | Unknown / unused |

**Byte 0 - button bitmask**
| Bit | Functionality |
| --- | --- |
| Bit 0 | Left button |
| Bit 1 | Right button |
| Bit 2 | Middle button |
| Bits 3-7 | Unknown/reserved |

(on this endpoint when a button is released the payload `00 00 00 00 00 00 00` is sent)

**Bytes 1-4 - movement**
- coordinate system works as follows:
  - `+X = right`
  - `-X = left`
  - `+Y = down`
  - `-Y = up`

**Byte 5 - Scroll wheel**
| Payload | Functionality |
| --- | --- |
| `00 00 00 00 00 01 00` | Up |
| `00 00 00 00 00 FF 00` | Down |

(just two known values, which makes deriving the format difficult)



### 0x82 Endpoint (HID; `bInterfaceProtocol = Keyboard`)
is used for custom button (for example DPI changes)
(on this endpoints a button release isn't captured)

- Data Length: 5 bytes

| Action	| Payload |
| --- | --- |
| DPI Up	| `07 01 82 05 00` |
| DPI Down	| `07 01 81 04 00` |
