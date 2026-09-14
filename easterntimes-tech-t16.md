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

**Report descriptor** (decoded with `hid-tools/hid-decode`)
```
# device 0:0
# 0x05, 0x01,                    // Usage Page (Generic Desktop)        0
# 0x09, 0x02,                    // Usage (Mouse)                       2
# 0xa1, 0x01,                    // Collection (Application)            4
# 0x09, 0x01,                    //  Usage (Pointer)                    6
# 0xa1, 0x00,                    //  Collection (Physical)              8
# 0x05, 0x09,                    //   Usage Page (Button)               10
# 0x19, 0x01,                    //   Usage Minimum (1)                 12
# 0x29, 0x05,                    //   Usage Maximum (5)                 14
# 0x15, 0x00,                    //   Logical Minimum (0)               16
# 0x25, 0x01,                    //   Logical Maximum (1)               18
# 0x75, 0x01,                    //   Report Size (1)                   20
# 0x95, 0x05,                    //   Report Count (5)                  22
# 0x81, 0x02,                    //   Input (Data,Var,Abs)              24
# 0x95, 0x03,                    //   Report Count (3)                  26
# 0x81, 0x01,                    //   Input (Cnst,Arr,Abs)              28
# 0x05, 0x01,                    //   Usage Page (Generic Desktop)      30
# 0x09, 0x30,                    //   Usage (X)                         32
# 0x09, 0x31,                    //   Usage (Y)                         34
# 0x16, 0x00, 0x80,              //   Logical Minimum (-32768)          36
# 0x26, 0xff, 0x7f,              //   Logical Maximum (32767)           39
# 0x75, 0x10,                    //   Report Size (16)                  42
# 0x95, 0x02,                    //   Report Count (2)                  44
# 0x81, 0x06,                    //   Input (Data,Var,Rel)              46
# 0x09, 0x38,                    //   Usage (Wheel)                     48
# 0x15, 0x80,                    //   Logical Minimum (-128)            50
# 0x25, 0x7f,                    //   Logical Maximum (127)             52
# 0x75, 0x08,                    //   Report Size (8)                   54
# 0x95, 0x01,                    //   Report Count (1)                  56
# 0x81, 0x06,                    //   Input (Data,Var,Rel)              58
# 0x05, 0x0c,                    //   Usage Page (Consumer Devices)     60
# 0x0a, 0x38, 0x02,              //   Usage (AC Pan)                    62
# 0x95, 0x01,                    //   Report Count (1)                  65
# 0x81, 0x06,                    //   Input (Data,Var,Rel)              67
# 0xc0,                          //  End Collection                     69
# 0xc0,                          // End Collection                      70
# 
R: 71 05 01 09 02 a1 01 09 01 a1 00 05 09 19 01 29 05 15 00 25 01 75 01 95 05 81 02 95 03 81 01 05 01 09 30 09 31 16 00 80 26 ff 7f 75 10 95 02 81 06 09 38 15 80 25 7f 75 08 95 01 81 06 05 0c 0a 38 02 95 01 81 06 c0 c0
N: device 0:0
I: 3 0001 0001
```

(In Wireshark the actual payload is 7 bytes in the 'Leftover Capture Data')

**Payload**
| Bytes | Functionality |
| --- | --- |
| Byte 0 | Buttons |
| Bytes 1-2 | X movement, signed 16-bit little-endian |
| Bytes 3-4 | Y movement, signed 16-bit little-endian |
| Byte 5 |	Scroll wheel,	signed 8-bit relative value (-128..127) |
| Byte 6 | AC-Pan (apparently unused by this mouse) |

**Byte 0 - button bitmask**
| Bit | Functionality |
| --- | --- |
| Bit 0 | Left button |
| Bit 1 | Right button |
| Bit 2 | Middle button |
| Bits 3-4 | Unknown/Buttons |
| Bits 5-7 | Padding/Constants |

(on this endpoint when a button is released the payload `00 00 00 00 00 00 00` is sent)

**Bytes 1-4 - movement**
- the data contains relative movement coordinates:
  - `+X = right`
  - `-X = left`
  - `+Y = down`
  - `-Y = up`

**Byte 5 - Scroll wheel**
| Payload | Functionality |
| --- | --- |
| `00 00 00 00 00 01 00` | Up |
| `00 00 00 00 00 FF 00` | Down |

(it seems like this field has just 3 states used by this mouse `up; down; no-action`)



### 0x82 Endpoint (HID; `bInterfaceProtocol = Keyboard`)
is used for custom button (for example DPI changes)
(on this endpoints a button release isn't captured)

- Data Length: 5 bytes

| Action	| Payload |
| --- | --- |
| DPI Up	| `07 01 82 05 00` |
| DPI Down	| `07 01 81 04 00` |
