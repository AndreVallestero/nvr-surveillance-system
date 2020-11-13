# nvr-surveillance-system
Notes for setting up a nvr-surveillance-system

Network Video Recorders (NVR) are different from Video Management Software (VMS), Video Surveillance Storage (VVS), Digital Video Recorders (DVR), and Central Management Software (CMS).

The other variants are ideal for Closed Circuit Television, where the video never leaves the camera and storage.

NVRs are meant to be network based (ingest pre-encoded video) and are capable of serving the video streams (past or present) to a user frontend (usually a web client). This is useful for people who want to monitor their cameras remotely over the internet.

- https://www.swann.com/blog/dvr-vs-nvr-whats-the-difference/
- https://ipvm.com/reports/vms-vs-cms

Total cost: ~$130USD for 4 cameras.

## Camera

Most important features in descending order:

1. ONVIF and RTSP: Open standards and protocols for interfacing with video streams.
2. Outdoor: Nightvision, water and weather proof, ideally +-40 ambient .
3. PoE: Power over ethernet, allows data and power transfer over a single cable. More secure than wireless, less network impact, lower latency, and better quality stream.

As of the time of writing this, the best value cameras are from AliExpress:

- $20 Smar H.264 HD: https://www.aliexpress.com/item/32911599471.html
- $21 Besder H.264 HD: https://www.aliexpress.com/item/32819313586.html

Many of these cameras operate at IP 192.168.x.10 with the following ports:

- 23/udp telnet
- 80/tcp web-server
- 517/udp talk
- 520/udp route
- 554/tcp rtsp
- 3343/udp ms-cluster-net
- 3702/udp ws-discovery
- 8000/tcp or 8899/tcp onvif
- 34567/tcp media configuration-setup-factory-cms

The default camera username is `admin`, there is no default password (empty)

To directly access the network stream through RTSP with VLC or other players, use the following address:

- Main: rtsp://192.168.1.10:554/user=admin&password=&channel=1&stream=0.sdp?real_stream--rtp-caching=100
- Lite: rtsp://192.168.1.10:554/user=admin&password=&channel=1&stream=1.sdp?real_stream--rtp-caching=100

Configuration of these cameras is done on the media port. The protocol is non-standard and proprietary. On mobile, the XmEye app can be used with limited configuration options. On Windows, H264 DVR CMS a.k.a `General_CMS_V3.1.0.4.T.20160218.exe` a.k.a. surf_launch.exe by Indigo Rose can configure all the camera settings and has further DVR capabilities.

H264 DVR CMS is available here:

- https://www.mediafire.com/file/tjvu3ou1txsv6kj/General_CMS_V3.1.0.4.T.20160218.exe/file
- https://drive.google.com/file/d/1HelNm6ERemxtINHxn35YiNrgFSN_aaxA/view?usp=sharing

## PoE Switch

To power your cameras, you can't use any network ethernet switch, you will need a PoE switch.

Most important features in descending order:

1. Voltage Support: Make sure that the PoE switch's voltage range covers your cameras. This tends to be 5V - 48V but sometimes only reaches a max of 30V so double check.
2. IEEE 802.3 af/at: Only sends power if the device requests it. Will not break devices that don't have PoE.
3. Integrated PSU: An integrated power supply is more efficient, more attractive, and takes less space.
4. Port Count: More ports means more cameras that can be connected.

As of the time of writing this, the best PoE switches are from Amazon:

- $45 MokerLink 4 Port PoE: https://www.amazon.ca/dp/B07SGBQNLM

## NVR Server Hardware

Any device with ethernet and USB3 that is capable of H264 decode is suitable as a NVR server. The cheapest option for this would be the Raspberry Pi 4 at $35.

## NVR Server Software

There are many options for NVR Server Software.

Most important features in descending order:

1. Good Web Client: A good client that is user friendly, responsive, works on any browser (even mobile), and is high performance.
2. Secure: Highly vetted, encrypted, and reliable. 
3. Open Source and Linux: Large community, and good support. Very important if running on a Single Board Computer like the Raspberry Pi 4.

Open source and Linux capable:
- [MotionEyeOS](https://github.com/ccrisan/motioneyeos)
- [MotionEye](https://github.com/ccrisan/motioneye)
- [ZoneMinder](https://github.com/ZoneMinder/ZoneMinder/)
- [iSpy](https://github.com/ispysoftware/iSpy)
- [Shinobi](https://github.com/ShinobiCCTV/Shinobi)
- [Kerberos.io](https://github.com/kerberos-io/kios)
- [Moonfire](https://github.com/scottlamb/moonfire-nvr)

Closed source:
- [Blue Iris](https://blueirissoftware.com/)
- [Ivideon Server](https://www.ivideon.com/)

The highest performance NVR is Moonfire which is capable of running 10, 1080p streams on a Raspberry Pi. This is possible because it doesn't re-encode the video, rather it simply stores the video that was pre-encoded by the cameras. Unfortunately, it's not actively developed and it has the smallest community of all these projects. Hopefully it picks up as its design philosophy is very promising.

Shinobi is similar to Moonfire in the sense that it allows for video-passthrough, but it's much more polished which makes it a better option for a low power systems.

MotionEye (and its OS version, MotionEyeOS) is the most popular open source NVR. It's build on GnuMotion, a well designed toolkit for network recording. It's especially popular because it's designed to run on lower-end hardware like the Raspberry Pi while providing many features. It's a good balance between Moonfire and ZoneMinder.

ZoneMinder is the most fully featured of the open-source NVRs. It's also less optimized than some of the others so it's recommended to run it on a more powerful hardware.

When it comes to closed-source Windows, Blue Iris is by far the best. Massive community, massive feature set, very reliable. There isn't much of a reason to use anything other closed-source Windows NVR.

## Storage

When storing the video, the micro sd card will likely not be enough. As such, buying a storage drive is highly recommended.

Most important features in descending order:

1. Peak Power Usage (Watts)
2. Reliability
3. Storage Size
4. Speed

When using a regular desktop, simply install it using a SATA power cable and a SATA3 data cable.

When using a Raspberry Pi, the storage drive must never draw more than 1.2A (6W @ 5V). If this happens, it will automatically fail to start.

Most 2.5 inch drives will stay under this limit so they are recommended for use. Hard drives require much more power when starting up as a result of the inertia of the spinning platter. As such, even if they are 2.5 inches, they may draw more than 1.2A.

Use a [USB3 to SATA3 connector](https://www.amazon.com/dp/B011M8YACM) to connect your drive to the device. If the drive does require more than 1.2A, you must power it externally using a [3.5 inch USB to SATA connector](https://www.amazon.com/dp/B07PVX682Q) which includes an external power supply.

## Additional Hardware

- 150+ ft (50m) of ethernet
    - Ensure Cat5[e] or better. If unsure, test if it's capable of gigabit speeds. If it is, it's probably capable of carrying the PoE required 
    - Ensure that it's pure copper, not aluminum. Aluminum cables conduct much worse and are horrible for signal integrity past a few meters.
    - Ensure calbe is round (twisted pair) cables instead of flat cables. Round cables retain better signal integrity, are less fragile, and are better suited for outdoors. Flat cables are also very diffuclt to crimp and cannot be crimped using standard RJ45 jacks.
    - Do NOT buy less than 45m (150ft) at a time. This is very wasteful. It's better to buy in bulk and cut to size. Ideally buy 1000ft at a time
- Crimper
    - Any crimper capable of RJ45 connectors should be suitable.
    - Wire strippers are a added bonus.
    - Can usually find in a set that includes JR45 connectors and an ethernet tester.
- RJ45 Connectors
    - After cutting the ethernet to size, you'll need connectors to connect them to.
    - Buy in bulk, at least 100.
    - Can usually find in a set that includes crimper and ethernet tester
- Ethernet Tester (optional)
    - Not too important, idealy get one that can test speeds, PoE, and individual wires.
    - Only get it if it's included in a set
