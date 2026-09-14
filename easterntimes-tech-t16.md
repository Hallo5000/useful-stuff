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
(on this endpoints a button release isn't captured and same goes for buttons which are not set to anything)

**Report descriptor** (decoded with `hid-tools/hid-decode`)
```
# device 0:0
# 0x05, 0x01,                    // Usage Page (Generic Desktop)        0
# 0x09, 0x06,                    // Usage (Keyboard)                    2
# 0xa1, 0x01,                    // Collection (Application)            4
# 0x85, 0x01,                    //  Report ID (1)                      6
# 0x05, 0x07,                    //  Usage Page (Keyboard)              8
# 0x19, 0xe0,                    //  Usage Minimum (224)                10
# 0x29, 0xe7,                    //  Usage Maximum (231)                12
# 0x15, 0x00,                    //  Logical Minimum (0)                14
# 0x25, 0x01,                    //  Logical Maximum (1)                16
# 0x75, 0x01,                    //  Report Size (1)                    18
# 0x95, 0x08,                    //  Report Count (8)                   20
# 0x81, 0x02,                    //  Input (Data,Var,Abs)               22
# 0x95, 0x06,                    //  Report Count (6)                   24
# 0x75, 0x08,                    //  Report Size (8)                    26
# 0x15, 0x00,                    //  Logical Minimum (0)                28
# 0x26, 0xff, 0x00,              //  Logical Maximum (255)              30
# 0x05, 0x07,                    //  Usage Page (Keyboard)              33
# 0x19, 0x00,                    //  Usage Minimum (0)                  35
# 0x2a, 0xff, 0x00,              //  Usage Maximum (255)                37
# 0x81, 0x00,                    //  Input (Data,Arr,Abs)               40
# 0xc0,                          // End Collection                      42
# 0x06, 0x0c, 0x00,              // Usage Page (Consumer Devices)       43
# 0x09, 0x01,                    // Usage (Consumer Control)            46
# 0xa1, 0x01,                    // Collection (Application)            48
# 0x85, 0x02,                    //  Report ID (2)                      50
# 0x25, 0x01,                    //  Logical Maximum (1)                52
# 0x15, 0x00,                    //  Logical Minimum (0)                54
# 0x75, 0x01,                    //  Report Size (1)                    56
# 0x0a, 0xb5, 0x00,              //  Usage (Scan Next Track)            58
# 0x0a, 0xb6, 0x00,              //  Usage (Scan Previous Track)        61
# 0x0a, 0xb7, 0x00,              //  Usage (Stop)                       64
# 0x0a, 0xcd, 0x00,              //  Usage (Play/Pause)                 67
# 0x0a, 0xe2, 0x00,              //  Usage (Mute)                       70
# 0x0a, 0xa2, 0x00,              //  Usage (Daily)                      73
# 0x0a, 0xe9, 0x00,              //  Usage (Volume Up)                  76
# 0x0a, 0xea, 0x00,              //  Usage (Volume Down)                79
# 0x95, 0x08,                    //  Report Count (8)                   82
# 0x81, 0x03,                    //  Input (Cnst,Var,Abs)               84
# 0x0a, 0x83, 0x01,              //  Usage (AL Consumer Control Config) 86
# 0x0a, 0x94, 0x01,              //  Usage (AL Local Machine Browser)   89
# 0x0a, 0x86, 0x01,              //  Usage (AL Spreadsheet)             92
# 0x0a, 0x88, 0x01,              //  Usage (AL Presentation App)        95
# 0x0a, 0x8a, 0x01,              //  Usage (AL Email Reader)            98
# 0x0a, 0x92, 0x01,              //  Usage (AL Calculator)              101
# 0x0a, 0xa8, 0x02,              //  Usage (Vendor Usage 0x2a8)         104
# 0x0a, 0x84, 0x01,              //  Usage (AL Word Processor)          107
# 0x95, 0x08,                    //  Report Count (8)                   110
# 0x81, 0x03,                    //  Input (Cnst,Var,Abs)               112
# 0x0a, 0x21, 0x02,              //  Usage (AC Search)                  114
# 0x0a, 0x23, 0x02,              //  Usage (AC Home)                    117
# 0x0a, 0x24, 0x02,              //  Usage (AC Back)                    120
# 0x0a, 0x25, 0x02,              //  Usage (AC Forward)                 123
# 0x0a, 0x26, 0x02,              //  Usage (AC Stop)                    126
# 0x0a, 0x27, 0x02,              //  Usage (AC Refresh)                 129
# 0x0a, 0x2a, 0x02,              //  Usage (AC Bookmarks)               132
# 0x0a, 0xb1, 0x02,              //  Usage (Vendor Usage 0x2b1)         135
# 0x95, 0x08,                    //  Report Count (8)                   138
# 0x81, 0x03,                    //  Input (Cnst,Var,Abs)               140
# 0xc0,                          // End Collection                      142
# 0x06, 0x00, 0xff,              // Usage Page (Vendor Defined Page 1)  143
# 0x09, 0x01,                    // Usage (Vendor Usage 1)              146
# 0xa1, 0x01,                    // Collection (Application)            148
# 0x85, 0x04,                    //  Report ID (4)                      150
# 0x15, 0x00,                    //  Logical Minimum (0)                152
# 0x26, 0xff, 0x00,              //  Logical Maximum (255)              154
# 0x09, 0x00,                    //  Usage (Undefined)                  157
# 0x75, 0x08,                    //  Report Size (8)                    159
# 0x95, 0x3a,                    //  Report Count (58)                  161
# 0xb1, 0x02,                    //  Feature (Data,Var,Abs)             163
# 0xc0,                          // End Collection                      165
# 0x06, 0x00, 0xff,              // Usage Page (Vendor Defined Page 1)  166
# 0x09, 0x01,                    // Usage (Vendor Usage 1)              169
# 0xa1, 0x01,                    // Collection (Application)            171
# 0x85, 0x06,                    //  Report ID (6)                      173
# 0x15, 0x00,                    //  Logical Minimum (0)                175
# 0x26, 0xff, 0x00,              //  Logical Maximum (255)              177
# 0x09, 0x00,                    //  Usage (Undefined)                  180
# 0x75, 0x08,                    //  Report Size (8)                    182
# 0x96, 0x78, 0x04,              //  Report Count (1144)                184
# 0xb1, 0x02,                    //  Feature (Data,Var,Abs)             187
# 0xc0,                          // End Collection                      189
# 0x06, 0x00, 0xff,              // Usage Page (Vendor Defined Page 1)  190
# 0x09, 0x01,                    // Usage (Vendor Usage 1)              193
# 0xa1, 0x01,                    // Collection (Application)            195
# 0x85, 0x07,                    //  Report ID (7)                      197
# 0x15, 0x00,                    //  Logical Minimum (0)                199
# 0x26, 0xff, 0x00,              //  Logical Maximum (255)              201
# 0x09, 0x00,                    //  Usage (Undefined)                  204
# 0x75, 0x08,                    //  Report Size (8)                    206
# 0x95, 0x04,                    //  Report Count (4)                   208
# 0x81, 0x00,                    //  Input (Data,Arr,Abs)               210
# 0xc0,                          // End Collection                      212
# 0x06, 0x00, 0xff,              // Usage Page (Vendor Defined Page 1)  213
# 0x09, 0x01,                    // Usage (Vendor Usage 1)              216
# 0xa1, 0x01,                    // Collection (Application)            218
# 0x85, 0x08,                    //  Report ID (8)                      220
# 0x15, 0x00,                    //  Logical Minimum (0)                222
# 0x26, 0xff, 0x00,              //  Logical Maximum (255)              224
# 0x09, 0x00,                    //  Usage (Undefined)                  227
# 0x75, 0x08,                    //  Report Size (8)                    229
# 0x95, 0x08,                    //  Report Count (8)                   231
# 0xb1, 0x02,                    //  Feature (Data,Var,Abs)             233
# 0xc0,                          // End Collection                      235
# 0x06, 0x00, 0xff,              // Usage Page (Vendor Defined Page 1)  236
# 0x09, 0x01,                    // Usage (Vendor Usage 1)              239
# 0xa1, 0x01,                    // Collection (Application)            241
# 0x85, 0x05,                    //  Report ID (5)                      243
# 0x15, 0x00,                    //  Logical Minimum (0)                245
# 0x26, 0xff, 0x00,              //  Logical Maximum (255)              247
# 0x09, 0x00,                    //  Usage (Undefined)                  250
# 0x95, 0x05,                    //  Report Count (5)                   252
# 0x75, 0x08,                    //  Report Size (8)                    254
# 0xb1, 0x02,                    //  Feature (Data,Var,Abs)             256
# 0xc0,                          // End Collection                      258
# 
R: 259 05 01 09 06 a1 01 85 01 05 07 19 e0 29 e7 15 00 25 01 75 01 95 08 81 02 95 06 75 08 15 00 26 ff 00 05 07 19 00 2a ff 00 81 00 c0 06 0c 00 09 01 a1 01 85 02 25 01 15 00 75 01 0a b5 00 0a b6 00 0a b7 00 0a cd 00 0a e2 00 0a a2 00 0a e9 00 0a ea 00 95 08 81 03 0a 83 01 0a 94 01 0a 86 01 0a 88 01 0a 8a 01 0a 92 01 0a a8 02 0a 84 01 95 08 81 03 0a 21 02 0a 23 02 0a 24 02 0a 25 02 0a 26 02 0a 27 02 0a 2a 02 0a b1 02 95 08 81 03 c0 06 00 ff 09 01 a1 01 85 04 15 00 26 ff 00 09 00 75 08 95 3a b1 02 c0 06 00 ff 09 01 a1 01 85 06 15 00 26 ff 00 09 00 75 08 96 78 04 b1 02 c0 06 00 ff 09 01 a1 01 85 07 15 00 26 ff 00 09 00 75 08 95 04 81 00 c0 06 00 ff 09 01 a1 01 85 08 15 00 26 ff 00 09 00 75 08 95 08 b1 02 c0 06 00 ff 09 01 a1 01 85 05 15 00 26 ff 00 09 00 95 05 75 08 b1 02 c0
N: device 0:0
I: 3 0001 0001
```
This interface uses multiple `Report ID`s:
| Report ID | Usage Page | Usage | Collection |
| --- | --- | --- | --- |
| 1 | Generic Desktop | Keyboard | Application |
| 2 | Consumer Devices | Consumer Control | Application |
| 4 | Vendor Defined Page 1 | Vendor Usage 1 | Application |
| 5 | Vendor Defined Page 1 | Vendor Usage 1 | Application |
| 6 | Vendor Defined Page 1 | Vendor Usage 1 | Application |
| 7 | Vendor Defined Page 1 | Vendor Usage 1 | Application |
| 8 | Vendor Defined Page 1 | Vendor Usage 1 | Application |



---
Out of Context: (from capturing traffic with wireshark)

- Data Length: 5 bytes

| Action	| Payload |
| --- | --- |
| DPI Up	| `07 01 82 05 00` |
| DPI Down	| `07 01 81 04 00` |
