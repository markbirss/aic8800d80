# Luckfox Lyra
<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/27d16309-3419-424b-8eab-fe3fdf70244e" />

```
sudo apt -y update; sudo apt -y install eject network-manager build-essential git make cmake iwconfig iw wireless-tools rfkill

installing linux-firmware will conflict with the dongle firmware (if you do install dongle the firmware again below)

need to reaplce again
sudo cp -r ./fw/aic8800D80 /lib/firmware/


root@forge1:~# lsusb
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 004: ID 368b:8d81 AICSemi AIC 8800D80
root@forge1:~# lsusb -tv
/:  Bus 001.Port 001: Dev 001, Class=root_hub, Driver=dwc2/1p, 480M
    ID 1d6b:0002 Linux Foundation 2.0 root hub
/:  Bus 002.Port 001: Dev 001, Class=root_hub, Driver=dwc2/1p, 480M
    ID 1d6b:0002 Linux Foundation 2.0 root hub
    |__ Port 001: Dev 004, If 0, Class=Vendor Specific Class, Driver=aic8800_fdrv, 480M
        ID 368b:8d81

root@forge1:~# iwconfig
lo        no wireless extensions.

can0      no wireless extensions.

end0      no wireless extensions.

end1      no wireless extensions.

wlx90de80d5be5b  unassociated  ESSID:""  Nickname:"AIC@8800"
          Mode:Auto  Frequency=2.412 GHz  Access Point: Not-Associated   
          Bit Rate:0 kb/s   
          Encryption key:off
          Link Quality=0/100  Signal level=-100 dBm  Noise level=0 dBm
          Rx invalid nwid:0  Rx invalid crypt:0  Rx invalid frag:0
          Tx excessive retries:0  Invalid misc:0   Missed beacon:0

usb0      no wireless extensions.

```

# AIC8800D80 Linux Driver
This driver is for the AIC8800D80 chipset, supported by devices such as the Tenda U11 and AX913B.

Added support for devices with Vendor ID 368B (tested).

Tested on Linux kernel 6.14 with Ubuntu 25.04 and 6.1.0.27 with Debian 12.

Bluetooth not working.

### Disclaimer
I did not develop this software, The code is sourced from the Tenda U11 driver. I only made some modifications to the code to adapt it to newer kernel versions. Apart from compilation issues, I am unable to address other problems.

### Attention
Before installing the driver, delete all aic8800-related folders under /lib/firmware. Using an incorrect firmware version may cause the system to freeze.

### Installation Steps
#### Copy udev rules:

Copy the aic.rules file to /lib/udev/rules.d/:

```bash
sudo cp aic.rules /lib/udev/rules.d/
```

#### Copy firmware:

Copy the aic8800D80 folder from ./fw to /lib/firmware/:

```bash
sudo cp -r ./fw/aic8800D80 /lib/firmware/
```
#### Navigate to the driver directory:

Change to the drivers/aic8800 directory:

```bash
cd ./drivers/aic8800
```

#### Compile and Install the Driver:

First, compile the driver:

```bash
make
```
Then, install the driver:

```bash
sudo make install
```

For any kernel updates, you'll need to reinstall the driver:

```bash
make clean
make
sudo make install
```

### Load the Driver
After installation, load the driver with the following command:

```bash
sudo modprobe aic8800_fdrv
```

### Verify the Module is Active
Check if the module is loaded correctly:

```bash
lsmod | grep aic
```
You should see output similar to:

```bash
aic8800_fdrv    536576  0
cfg80211        1146880 1   aic8800_fdrv
aic_load_fw     69632   1   aic8800_fdrv
usbcore         348160  10  xhci_hcd,ehci_pci,usbhid,usb_storage,ehci_hcd,xhci_pci,uas,aic_load_fw,uhci_hcd,aic8800_fdrv
```

After that, plug in your USB wireless network card.

### Verify Wi-Fi Device is Active
To check if the Wi-Fi interface is recognized, run:

```bash
iwconfig
```
If the device is still not active, check the kernel logs for any errors related to the driver:

```bash
sudo dmesg
```
