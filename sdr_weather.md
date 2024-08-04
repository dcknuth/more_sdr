# SDR Weather Station Info
Can I pull in temperature information from a neighbor’s weather station? I have seen some in the neighborhood, but they are a little far away. Could I still pick up the signal?

First it seems I will need some additional tooling and testing of the setup.
```
sudo apt install soapysdr-tools
sudo apt install rtl-433
SoapySDRUtil --find
rtl_433 -d "device=hackrf" -g AMP=1,LNA=33,VGA=62 -f 433.92M -f 434M -f 315M -H 30 -C si -s 2000k
```
Soapysdr lets you use a HackRF and the third command tells you if it is found. The fourth should set the device to match what showed up in the third and start scanning the 433 and 315 MHz frequencies at 30 second intervals. It seems to go OK, but is not finding anything. I have a better antenna on the way, but let’s try the RTL-SDR.COM v3 while we wait. Since this is the device that rtl_433 expects, all we have to do is run it plain without any parameters and we see:
```
dave@dave-RF:~$ rtl_433
rtl_433 version unknown inputs file rtl_tcp RTL-SDR SoapySDR
Use -h for usage help and see https://triq.org/ for documentation.
Trying conf file at "rtl_433.conf"...
Trying conf file at "/home/dave/.config/rtl_433/rtl_433.conf"...
Trying conf file at "/usr/local/etc/rtl_433/rtl_433.conf"...
Trying conf file at "/etc/rtl_433/rtl_433.conf"...
Registered 122 out of 149 device decoding protocols [ 1-4 8 11-12 15-17 19-21 23 25-26 29-36 38-60 63 67-71 73-100 102-105 108-116 119 121 124-128 130-149 ]
Detached kernel driver
Found Rafael Micro R820T tuner
Exact sample rate is: 250000.000414 Hz
[R82XX] PLL not locked!
Sample rate set to 250000 S/s.
Tuner gain set to Auto.
Tuned to 433.920MHz.
Allocating 15 zero-copy buffers
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
time      : 2021-10-14 13:12:17
model     : Ambientweather-F007TH                  House Code: 35
Channel   : 1            Battery   : 1             Temperature: 68.4 F
Humidity  : 53 %         Integrity : CRC
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
time      : 2021-10-14 13:13:10
model     : Ambientweather-F007TH                  House Code: 35
Channel   : 1            Battery   : 1             Temperature: 68.4 F
Humidity  : 53 %         Integrity : CRC
```
Well that’s pretty great. We see one neighbor has a weather station and we are picking it up with everything set at the defaults. Wait, is the $30 RTL-SDR.COM more sensitive than the $350 HackRF? A little searching seems to confirm that yes, the RTL-SDR.COM probably is a good bit more sensitive in most cases. The HackRF does get you 10x the sample rate and wider (both higher and lower) frequencies and transmit, but don’t expect greater sensitivity.

The 433MHz specific antenna arrived with a number of other things, including yet another SDR the Nesdr Smart. The antenna seems about the same as the dipole, but the Nesdr Smart seems even more sensitive than the RTL-SRD.COM and now I can see two different sensors that the neighbors have. I am going to call it there for this particular investigation. Back to some general Gnu Radio learning before picking a new item to try.

UPDATE: I tried to get the antenna up in the air about 18 ft, outside the house and then ran the scan again. This time it picked up a fair amount more. Here are the different sensors that seemed to show up (duplicate reports removed)
```
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
time      : 2021-11-02 13:29:01
model     : Schrader     type      : TPMS          flags     : 27            ID        : 0380E6A
Pressure  : 340.0 kPa    Temperature: 25 C         Integrity : CRC
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
time      : 2021-11-02 13:29:12
model     : Schrader     type      : TPMS          flags     : 26            ID        : 0380CD5
Pressure  : 307.5 kPa    Temperature: 27 C         Integrity : CRC
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
time      : 2021-11-02 13:29:19
model     : Schrader     type      : TPMS          flags     : 27            ID        : 03805BF
Pressure  : 327.5 kPa    Temperature: 27 C         Integrity : CRC
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
time      : 2021-11-02 13:30:57
model     : inFactory-TH ID        : 104
Channel   : 1            Temperature: 68.30 °F    Humidity  : 69 %
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
time      : 2021-11-02 13:31:30
model     : Acurite-606TX id        : 165
Battery   : 1            Temperature: 24.5 C       Integrity : CHECKSUM
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
time      : 2021-11-02 13:32:37
model     : Ambientweather-F007TH                  House Code: 35
Channel   : 1            Battery   : 1             Temperature: 68.2 F       Humidity  : 69 %
Integrity : CRC
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
time      : 2021-11-02 13:32:59
model     : Ambientweather-F007TH                  House Code: 224
Channel   : 2            Battery   : 1             Temperature: 70.2 F       Humidity  : 59 %
Integrity : CRC
```
I think the three 'Schrader' sensors are actually tire pressure sensors