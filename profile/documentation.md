## [bsbf-client-openwrt-firmware-selector](https://github.com/bondingshouldbefree/bsbf-client-openwrt-firmware-selector)

This repository contains the BSBF OpenWrt Firmware Selector. BSBF OFS provides a website to build and retrieve OpenWrt firmware with the BSBF bonding solution client.

The directive:
- List stable releases and snapshot, and default to the latest stable release.
- Add the bonding packages to the ASU extra packages list.
- Only have the Attendedsysupgrade (ASU) section.
- Look for the server-ipv4, server-port, and uuid arguments. All arguments are required.
- Have a Request Credentials button to retrieve the arguments from bsbf-add-client-api.
- Generate a uci-defaults script from the given arguments and add it to the top of the uci-defaults script the user may optionally provide.
- Make ASU request to https://sysupgrade.openwrt.org with the uci-defaults script and packages list.
- Deploy the website to GitHub Pages to https://fs.bondingshouldbefree.com/.

## [bsbf-resources](https://github.com/bondingshouldbefree/bsbf-resources)

This repository contains the resources for the BSBF bonding solution.

### Client Resources

- `bsbf-client-web/`:
  - BSBF Client Monitor. Provides a website to monitor interfaces, enable or disable bonding, do rate-limiting, do backup declaration, and run speed tests.
- `99-bsbf-bonding.conf`:
  - systemd configuration for xray-core for conventional Linux distributions. It configures routing in conjunction with nftables and xray-core, and applies the bsbf_bonding.nft nftables script.
- `bsbf-bonding`:
  - This script provides an interface to manage the bonding solution for conventional Linux distributions.

    ```
    Usage: bsbf-bonding --status | --enable | --disable | --uninstall
    ```
- `bsbf-client-installer.sh`:
  - The BSBF bonding solution client installer for conventional Linux distributions.

    ```
    Usage: ./bsbf-client-installer.sh --server-ipv4 <ADDR> --server-port <PORT>
           --uuid <UUID>
    ```

    Run as root.

    Only Linux distributions with apt and systemd are supported.

    Resources the BSBF bonding solution client will use:
    - TCP port 8080.
    - Packet marks 1 and 2.
    - Metrics 9 to 16.
- `bsbf-client-openwrt-installer.sh`:
  - The BSBF bonding solution client installer for OpenWrt.

    ```
    Usage: ./bsbf-client-openwrt-installer.sh --server-ipv4 <ADDR> --server-port
           <PORT> --uuid <UUID>
    ```

    Run as root.

    Resources the BSBF bonding solution client will use:
    - Packet marks 1 and 2.
    - Metrics 1 to [WAN Interface Count] and 9 to 16.
- `bsbf-mptcp`:
  - This script tracks connectivity and MPTCP support of interfaces, maintains the MPTCP endpoint table, and manages routes. It uses the metric values 9-16 for managing routes.
- `bsbf-mptcp-helper`:
  - This is a helper script that provides the ability to manipulate an MPTCP endpoint of an interface. The add function adds an MPTCP endpoint(s) of a given interface as a subflow. The remove function removes an MPTCP endpoint(s) of a given interface. The backup and nobackup function changes the MPTCP endpoint(s) of a given interface to a backup or nobackup.
- `bsbf-mptcp.service`:
  - The systemd service for bsbf-mptcp for conventional Linux distributions.
- `bsbf-netspeed`:
  - This script displays the download and upload throughput of a given interface(s) at a given interval.
- `bsbf-quectel-usbnet`:
  - This script changes the usbnet mode on Quectel modems to MBIM and then reboots the system.
- `bsbf-rate-limiting`:
  - This script sets a given download and upload rate limit on a given interface.
- `bsbf_bonding.nft`:
  - The nftables script to transparently proxy all traffic to xray-core. All DNS traffic except the traffic coming out of the loopback interface is proxied to xray-core. Packets from client destined to an IPv4 address that is local to the system bypass the proxy. This includes unicast DHCP Request packets with the IPv4 daddr not being a private IPv4 address, for which we had to put the 'udp dport 67' rule to have it bypass the proxy. This rule ensures that all traffic from client that is destined to the router bypasses the proxy. This covers the case that if the LAN IP address of the router is not a private IPv4 address, it wouldn't bypass the proxy.

    This makes it so that all packets that are supposed to be routed will be tproxied, except packets with a private IPv4 address as daddr.
- `lighttpd-bsbf-client-web.conf`:
  - lighttpd web server configuration to host BSBF Client Monitor for conventional Linux distributions. The web server listens on 0.0.0.0:8080.
- `mikrotik-netboot.sh`:
  - This script configures the system to load an OpenWrt firmware to a Mikrotik device. Run as root.
- `xray.json`:
  - The xray-core configuration. The dokodemo-door protocol is used as an inbound to process transparently proxied traffic. The vless protocol is used as an outbound to communicate with the server. DNS traffic is sent to 1.1.1.1 over TCP via the vless outbound.

### Server Resources

- `99-bsbf-bonding.conf`:
  - systemd configuration for xray-core. It includes the core-config for xray-core. The core-config file is supplied first to have the blackhole outbound as the second outbound.
- `99-bsbf-rate-limiting`:
  - The if-up.d script that runs bsbf-rate-limiting when a matching network interface comes up.
- `bsbf-add-client`:
  - This script adds a client to the system. Run as root.
- `bsbf-add-client-api`:
  - This NodeJS programme provides an API to add a client to the system. Run as root.
- `bsbf-add-client-api.service`:
  - The systemd service for bsbf-add-client-api.
- `bsbf-generate-openwrt-image.sh`:
  - This script generates an OpenWrt image with the BSBF bonding solution client using OpenWrt's Attendedsysupgrade or a local imagebuilder.
- `bsbf-list-client`:
  - This script retrieves a list of existing clients. Run as root.
- `bsbf-mptcp-configuration.service`:
  - The systemd service to configure MPTCP. Sets subflow limit to 8. Disables blackhole timeout. Disables fallback to TCP.
- `bsbf-rate-limiting`:
  - This script loads rate-limiting for existing clients on a given interface. Run as root.
- `bsbf-remove-client`:
  - This script removes a client from the system by a given port, port range, or UUID. Run as root.
- `bsbf-server-installer.sh`:
  - The BSBF bonding solution server installer for conventional Linux distributions.

    ```
    Usage: ./bsbf-server-installer.sh [--client-limit <COUNT>] | --uninstall
    ```

    Only Linux distributions with apt, systemd, and ifupdown are supported.

    A maximum of 16384 clients can be added by default. To change this, use `--client-limit`.

    Resources the BSBF bonding solution server will use:
    - TCP ports 16383 to [16384 + Client Limit - 1].
    - Packet marks 16384 to [16384 + Client Limit - 1] and 32768 to [32768 + Client Limit - 1].
    - TC HTB classids 16384 to [16384 + Client Limit - 1] and 32768 to [32768 + Client Limit - 1].

    The bsbf-add-client-api systemd service is not enabled or started by default.
- `core-config.json`:
  - Core configuration of xray-core:
    - Prevent access to local IP addresses.
- `worker.js`:
  - Cloudflare Worker source code to forward bsbf-add-client-api requests to the nearest server.

## [openwrt](https://github.com/bondingshouldbefree/openwrt)

This repository is a fork of OpenWrt. This repository is used for submitting patches developed by the BondingShouldBeFree organisation. The bsbf branch is used for compiling test images.

## [packages](https://github.com/bondingshouldbefree/packages)

This repository is a fork of OpenWrt's packages feed. This repository is used for submitting patches developed by the BondingShouldBeFree organisation. The bsbf branch is used for hosting test packages.

### bsbf-openwrt-resources

The multi-package that contains the OpenWrt-specific resources for the BSBF bonding solution client.

- `files/etc/hotplug.d/net/99-bsbf-autoconf-dhcp`:
  - The hotplug script that creates a network with a DHCP client using a newly created network interface. It uses metric values from 1 to 8.
- `files/etc/hotplug.d/net/99-bsbf-autoconf-cellular`:
  - The hotplug script that creates a network with MBIM or QMI protocol using a newly created network interface. It uses metric values from 1 to 8.

### bsbf-resources

The multi-package that contains the generic and OpenWrt-specific resources for the BSBF bonding solution client.

- `files/etc/init.d/bsbf-bonding-nft`:
  - The procd init script for applying bsbf_bonding.nft at boot.
- `files/etc/init.d/bsbf-mptcp`:
  - The procd init script for bsbf-mptcp.
- `files/etc/init.d/bsbf-quectel-usbnet`:
  - The procd init script for bsbf-quectel-usbnet.
- `files/etc/uci-defaults/99-bsbf-bonding`:
  - This script configures the system for the BSBF bonding solution client. Every found WAN interface is given a metric value starting from 1.
- `files/usr/sbin/bsbf-bonding`:
  - This script provides an interface to manage the bonding solution.

### Test packages

- `meig_firehose`:
  - This was provided to us by MeiG Smart to upgrade the firmware of their cellular modems.
- `qfirehose`:
  - Firmware upgrade tool for Quectel cellular modems. This was removed from OpenWrt's packages feed due to restrictive licencing. I'll let it stay here.
- `shadowsocks-go`:
  - Another potential proxy programme for the BSBF bonding solution.
- `shoes`:
  - Another potential proxy programme for the BSBF bonding solution. Currently patching it to apply a PR that fixes compiling the aws-lc-sys dependency.

## Notes

- The MPTCP implementation has got a logical limit of bonding a maximum of 8 paths.

- On bsbf-mptcp, the fping command can be replaced with `ncat -z -w 3 -4 -s "$IFACE_ADDR" check.mptcp.dev 80` if running on a network with only TCP support. This consumes about four times more data than pinging (~412 B/s vs ~102 B/s), therefore this is not used by default. You'll also need to retrieve the IP address of the interface as ncat doesn't support binding to interface.

- OpenWrt package list to support certain setups:
  - Dongle modems:
    - `bsbf-autoconf-dhcp kmod-usb-net-cdc-ether usb-modeswitch`
  - MBIM modems:
    - `bsbf-autoconf-cellular bsbf-quectel-usbnet kmod-usb-serial-option umbim`
  - USB adapters and Android USB tethering:
    - `bsbf-autoconf-dhcp kmod-usb-net-cdc-ether kmod-usb-net-rtl8152 kmod-usb-net-rndis`
  - iOS USB tethering:
    - `bsbf-autoconf-dhcp kmod-usb-net-ipheth usbmuxd`
  - Diagnostics:
    - `bsbf-netspeed curl htop ss kmod-inet-mptcp-diag`
