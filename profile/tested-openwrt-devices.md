## Tested Hardware

- Cudy R700 (128MB RAM)
- GL.iNet AX1800 (512MB RAM)
- GL.iNet AXT1800 (512MB RAM)
- Banana Pi BPI-R4 (4GB RAM)
- Mikrotik RBM33G (256MB RAM)
- Mikrotik RB960PGS (128MB RAM)

## Notes

- GL.iNet first time OpenWrt image installation procedure: Hold reset button and plug into power. Wait for 5 blue LED flashes until LED turns white. Add IPv4 address 192.168.1.2 and head to 192.168.1.1. Install OpenWrt .ubi image.

- Mikrotik first time OpenWrt image installation procedure: Download an initramfs image for the device. Run the [mikrotik-netboot.sh](https://github.com/bondingshouldbefree/bsbf-resources/blob/main/resources-client/mikrotik-netboot.sh) script as root on a Linux distro with dnsmasq installed. Turn off distro's network profile for the network interface so it won't interfere with the IP addressing of the interface.
