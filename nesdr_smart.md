# SDR with a Nesdr Smart
Now I have a third SDR, the Nesdr Smart. It came with a Ham It Up and a selection of additional antennas and connectors. To get it working on Windows is basically the same as the RTL-SDR.COM with the issue that SDRSharp does not seem to work with that hardware. However, CubicSDR will work with it and it seems to do great with the default config.

Over to the Linux VM. You will have to setup another USB pass through for the virtual machine before starting it (covered previously). Once the machine is running, you will need to disable the driver selected by default and then since we already installed rtl-sdr, it will work right away. You can follow the instructions [here](https://www.nooelec.com/store/downloads/dl/file/id/72/product/294/nesdr_installation_manual_for_ubuntu.pdf). This is what I did:
```
lsusb #To show the usb devices and see that they have the wrong driver
lsmod | grep dvb #Same purpose
sudo echo "blacklist dvb_usb_rtl28xxu" > /etc/modprobe.d/blacklist-dvb.conf
rtl_test #To see if the device now works
```
After this I could run gqrx and rtl_433 just fine.
