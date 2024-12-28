# Notes on the Fender Mustang Micro Plus

The [Fender Mustang Micro Plus](https://www.fender.com/en-US/guitar-amplifiers/contemporary-digital/mustang-micro-plus/2311600000.html) is a headphone amp with built in amp simulators and effects controlled over bluetooth using the [Fender Tone](https://support.fender.com/en-US/knowledgebase/category/?id=CAT-01041) app on iOS and Android.

It is a neat toy and I was curious if anyone had investigated it further so I searched online and found a Linux client app for older fender amps - [offa/plug](https://github.com/offa/plug). Reading [this comment](https://github.com/offa/plug/issues/25#issuecomment-2420879538) on an issue I got curious and decided to poke a little at the app etc.

This repo contains rough notes, and anything else I dig up while investigating my Mustang Micro Plus.

Note - this is not an official Fender repository, I am not claiming copyright/trademark etc on anything in this repository. 

## Rough Notes ...

- Downloaded `Fender Tone_4.0.5.103596_APKPure.xapk`
- Extracted it and found multiple additional apks
 - `com.fender.tone.apk`
   - Extracted `com.fender.tone.apk` and found a `classes.dex` android java app
   - Decompiled `classes.dex` using [jadx](https://github.com/skylot/jadx)
    - includes code referencing [Qualcomm GAIA](https://cweiske.de/tagebuch/bluetooth-gaia.htm) bluetooth protocol
    - uses [CCL framework](https://ccl.devl) which has not yet been fully opensourced yet ([github.com/cclsoftware](https://github.com/cclsoftware))
    - references to [GATT](https://learn.adafruit.com/introduction-to-bluetooth-low-energy/gatt)
    - Main java entry point appears to load a native app
  - `config.arm64_v8a.apk` contains multiple lib\*.so files
    - Skimmed .so's in [Ghidra](https://ghidra-sre.org/) ...
    - `libmustangdevice.so` uses lz4 & png functions, has protocol buffer code, imports `libucnet.so` 
       - extracted protobufs to [protobuf](./protobuf) using [schdub/protodec](`Fender Tone_4.0.5.103596_APKPure.xapk`)
       - based on a quick skim this is different to the [LT AMP series](https://github.com/brentmaxwell/LtAmp/blob/main/Schema/protobuf/) but has some similarities

## Connecting via usb
Connecting it to my linux laptop I get the following in dmesg:
```
[ 8369.868291] usb 1-3: new full-speed USB device number 6 using xhci_hcd
[ 8369.992751] usb 1-3: New USB device found, idVendor=1ed8, idProduct=003a, bcdDevice= 0.13
[ 8369.992758] usb 1-3: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[ 8369.992761] usb 1-3: Product: Mustang Micro Plus
[ 8369.992763] usb 1-3: Manufacturer: Fender
[ 8369.992765] usb 1-3: SerialNumber: <REDACTED>
[ 8370.071184] usbcore: registered new interface driver snd-usb-audio
```

Full lsusb verbose output in [lsusb\_verbose.txt](./lsusb_verbose.txt)

There are currently [no firmware updates available](https://fendercustomersupport.microsoftcrmportals.com/en-us/knowledgebase/article/KA-02267) so I'm not sure how doable it is to access anything else useful over USB currently.


## references
* https://medium.com/fender-engineering/c-desktop-application-architecture-for-digital-amplifier-connectivity-454da5c44026
* https://github.com/brentmaxwell/LtAmp/blob/main/Docs/Protocol.md
