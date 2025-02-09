## [bsbf-client-openwrt-firmware-selector](https://github.com/bondingshouldbefree/bsbf-client-openwrt-firmware-selector)

This repository contains the BSBF OpenWrt Firmware Selector. The firmware selector provides a website to build and retrieve OpenWrt firmware with the BSBF bonding solution client.

The directive:
- List stable releases and snapshot, and default to the latest stable release.
- Only have the Attendedsysupgrade (ASU) section.
- Look for server-ipv4, server-port, uuid, and ocg arguments, with the former three as required arguments.
- Have a Request Credentials button to retrieve the required arguments from an API.
- Supply given arguments to bsbf-client-openwrt-config-generator. Store the generated uci-defaults script and packages list.
- Insert the uci-defaults script and packages list to the top of the uci-defaults script and packages list the user may optionally provide.
- Make ASU request to https://sysupgrade.openwrt.org with the uci-defaults script and packages list.
- Deploy the website to GitHub Pages to https://fs.bondingshouldbefree.com/.

## [bsbf-openwrt-feed](https://github.com/bondingshouldbefree/bsbf-openwrt-feed)

The BSBF OpenWrt feed for development and testing purposes.

- `meig_firehose`:
  - This was provided to us by MeiG Smart to upgrade the firmware of their cellular modems.
- `qfirehose`:
  - Firmware upgrade tool for Quectel cellular modems. This was removed from OpenWrt's packages feed due to restrictive licencing. I'll let it stay here.
- `shadowsocks-go`:
  - Another potential proxy programme for the BSBF bonding solution.
- `shoes`:
  - Another potential proxy programme for the BSBF bonding solution. Currently patching it to apply a PR that fixes compiling the aws-lc-sys dependency.

Add bsbf_openwrt_feed feed to the bottom of `feeds.conf.default`:

```
src-git bsbf_openwrt_feed https://github.com/bondingshouldbefree/bsbf-openwrt-feed.git
```

Refresh feeds and install bsbf_openwrt_feed feed:

```
./scripts/feeds update && ./scripts/feeds install -a
```

## [bsbf-perf-test](https://github.com/bondingshouldbefree/bsbf-perf-test)

This repository contains scripts that run the BSBF bonding solution in certain conditions to test the performance.

```
Usage: ./bsbf-perf-test.sh <u32|ranged-flower|flower|no-matching|
                            disable-gso-gro|default>

Usage: ./bsbf-perf-proxy-test.sh <fw-u32|fw-flower|u32-u32|u32-flower|
                                  no-matching|disable-gso-gro|default>
                                 <sing-box-tun|sing-box-tproxy|v2ray-tproxy|
                                  xray-tun|xray-tproxy>
tc filter egress-ingress: fw-u32|fw-flower|u32-u32|u32-flower
```

Run as root.

Install [tcp-in-udp](https://github.com/multipath-tcp/tcp-in-udp) before running the scripts.

For running on OpenWrt, install these packages:

```
tcp-in-udp ethtool ip-full tc-full kmod-sched kmod-sched-flower kmod-sched-bpf kmod-nft-tproxy kmod-veth coreutils-nproc iperf3 xray-core v2ray-core sing-box-tiny
```

Head here for [the test results of some hardware](bsbf-perf-test-results.md).

## [bsbf-resources](https://github.com/bondingshouldbefree/bsbf-resources)

This repository contains the resources for the BSBF bonding solution.

### Client Resources

- `99-bsbf-bonding.conf`:
  - systemd configuration for xray-core for conventional Linux distributions. It configures routing in conjunction with nftables and xray-core, and applies the bsbf_bonding.nft nftables script.
- `99-bsbf-plpmtu.sh`:
  - NetworkManager dispatcher script for conventional Linux distributions. It runs bsbf-plpmtu when a matching network interface comes up.
- `99-bsbf-tcp-in-udp.sh`:
  - NetworkManager dispatcher script for conventional Linux distributions. It runs bsbf-tcp-in-udp when a matching network interface comes up.
- `bsbf-client-installer.sh`:
  - The BSBF bonding solution client installer for conventional Linux distributions.

    ```
    Usage: ./bsbf-client-installer.sh --server-ipv4 <ADDR> --server-port <PORT>
           --uuid <UUID> | --uninstall
    ```

    Run as root.

    Only Linux distributions with apt, systemd, and NetworkManager are supported for now.

    Packet mark 2 will be used on the system.
- `bsbf-client-openwrt-config-generator.sh`:
  - This script generates a uci-defaults script and a list of packages which can be used with OpenWrt's imagebuilder to build an image with the BSBF bonding solution client.

    ```
    Usage: ./bsbf-client-openwrt-config-generator.sh --server-ipv4 <ADDR>
           --server-port <PORT> --uuid <UUID>
    ```

    Once the script is run, the package list and the uci-defaults script can be used as below to request an image from OpenWrt's firmware-selector:

    ```
    jq -Rs '
    {
      defaults: .,
      packages: [
        "bsbf-bonding",
        "kmod-sched",
        "kmod-sched-bpf",
        "kmod-nft-tproxy",
        "-luci"
      ],
      profile: "mikrotik_routerboard-m33g",
      target: "ramips/mt7621",
      version: "25.12.1"
    }
    ' 99-bsbf-bonding-2 |
    curl -X POST https://sysupgrade.openwrt.org/api/v1/build \
      -H "Content-Type: application/json" \
      -d @-
    ```

    Or supplied to imagebuilder with `files/etc/uci-defaults/99-bsbf-bonding-2`:

    ```
    PACKAGES=$(./bsbf-client-openwrt-imagebuilder-config-generator.sh --server-ipv4 <ADDR> --server-port <PORT> --uuid <UUID>) && \
    install -D 99-bsbf-bonding-2 ../openwrt-imagebuilders/openwrt-imagebuilder-25.12.1-ramips-mt7621.Linux-x86_64/files/etc/uci-defaults/99-bsbf-bonding-2 && \
    cd ../openwrt-imagebuilders/openwrt-imagebuilder-25.12.1-ramips-mt7621.Linux-x86_64 && \
    make image \
    PROFILE="mikrotik_routerboard-m33g" \
    PACKAGES="$PACKAGES" \
    FILES="files"
    ```
- `bsbf-client-openwrt-installer.sh`:
  - The BSBF bonding solution client installer for OpenWrt.

    ```
    Usage: ./bsbf-client-openwrt-installer.sh --server-ipv4 <ADDR> --server-port
           <PORT> --uuid <UUID> | --uninstall
    ```

    Run as root.

    Packet mark 2 will be used on the system.
- `bsbf-mptcp`:
  - This script pings a given IPv4 address(es) a given amount of times on an interface(s) which is a default route. Depending on whether there's connectivity and the average round-trip time, it adds or removes an affine subflow and makes it a backup or a nobackup.
- `bsbf-mptcp-helper`:
  - This is a helper script that provides the ability to manipulate an MPTCP endpoint of an interface. The add function adds an MPTCP endpoint(s) of a given interface as a subflow. The remove function removes an MPTCP endpoint(s) of a given interface or a removed interface. The backup/nobackup function changes the MPTCP endpoint(s) of a given interface to a backup or nobackup.
- `bsbf-mptcp.service`:
  - The systemd service for bsbf-mptcp for conventional Linux distributions.
- `bsbf-netspeed`:
  - This script displays the download and upload throughput of a given interface(s) at a given interval.
- `bsbf-quectel-usbnet`:
  - This script changes the usbnet mode on Quectel modems to MBIM and then reboots the system.
- `bsbf-rate-limiting`:
  - This script sets a given download and upload rate limit on a given interface.
- `bsbf-route`:
  - This script changes the preferred default route until there's access to a given IPv4 address. This script uses the metric values 9-100.
- `bsbf-route.service`:
  - The systemd service for bsbf-route for conventional Linux distributions.
- `bsbf-tcp-in-udp`:
  - This script loads the TCP-in-UDP BPF programme on a given interface.
- `bsbf_bonding.nft`:
  - The nftables script for conventional Linux distributions to transparently proxy all traffic to xray-core.
- `mikrotik-netboot.sh`:
  - This script configures the system to load an OpenWrt firmware to a Mikrotik device. Run as root.
- `xray.json`:
  - The xray-core configuration. The dokodemo-door protocol is used as an inbound to process transparently proxied traffic. The vless protocol is used as an outbound to communicate with the server.

### Server Resources

- `99-bsbf-bonding.conf`:
  - systemd configuration for xray-core. It includes the core-config for xray-core. The core-config file is supplied first to have the blackhole outbound as the second outbound.
- `99-bsbf-rate-limiting`:
  - The if-up.d script that runs bsbf-plpmtu when a matching network interface comes up.
- `99-bsbf-rate-limiting`:
  - The if-up.d script that runs bsbf-rate-limiting when a matching network interface comes up.
- `99-bsbf-tcp-in-udp`:
  - The if-up.d script that runs bsbf-tcp-in-udp when a matching network interface comes up.
- `bsbf-add-client`:
  - This script adds a client to the system. Run as root.
- `bsbf-add-client-api`:
  - This NodeJS programme provides an API to add a client to the system. Run as root.
- `bsbf-add-client-api.service`:
  - The systemd service for bsbf-add-client-api.
- `bsbf-compile-openwrt-image.sh`:
  - Put this script in the root directory of an OpenWrt SDK source code to use it.
- `bsbf-generate-openwrt-image.sh`:
  - This script generates an OpenWrt image with the BSBF bonding solution client.
- `bsbf-list-client`:
  - This script retrieves a list of existing clients. Run as root.
- `bsbf-mptcp-set-subflow-limit.service`:
  - The systemd service to set MPTCP subflows limit to 8.
- `bsbf-rate-limiting`:
  - This script loads rate-limiting for existing clients on a given interface. Run as root.
- `bsbf-remove-client`:
  - This script removes a client from the system by a given port, port range, or UUID. Run as root.
- `bsbf-server-installer.sh`:
  - The BSBF bonding solution server installer for conventional Linux distributions.

    ```
    Usage: ./bsbf-server-installer.sh [--client-limit <COUNT>] | --uninstall
    ```

    Only Linux distributions with apt, systemd, and ifupdown are supported for now.

    A maximum of 16384 clients can be added by default. To change this, use `--client-limit`. The limit count must be a power of 2: 1, 2, 4, ..., 16384.

    Resources the BSBF bonding solution server will use:
    - UDP port 16383.
    - TCP and UDP ports 16384 to 16384 + Client Limit - 1.
    - Packet marks (16384 to 16384 + Client Limit - 1) and (32768 to 32768 + Client Limit - 1).
    - TC HTB classids (16384 to 16384 + Client Limit - 1) and (32768 to 32768 + Client Limit - 1).

    The bsbf-add-client-api systemd service is not enabled or started by default.
- `bsbf-tcp-in-udp`:
  - This script loads the TCP-in-UDP BPF programme on a given interface. Run as root.
- `core-config.json`:
  - Core configuration of xray-core:
    - Prevent access to local IP addresses.
    - Lower connIdle from 300 to 10 to prevent running out of memory on computers with small memory.

### Shared Resources

- `bsbf-plpmtu`:
  - This script finds the PMTU of a given interface, when there's internet, and sets it on the interface. Run as root.

## [bsbf-teltonika-resources](https://github.com/bondingshouldbefree/bsbf-teltonika-resources)

This repository contains the resources for the BSBF bonding solution client for Teltonika. This repository can also be used as an OpenWrt feed.

### Client Resources

- `99-bsbf-bonding`:
  - The uci-defaults script to configure the system for the BSBF bonding solution client.
- `firewall.user`:
  - The firewall3 script to transparently proxy all traffic to xray-core.

### Teltonika Feed

- `bsbf-openwrt-resources`:
  - Refer to OpenWrt packages documentation below.
- `bsbf-resources`:
  - Refer to OpenWrt packages documentation below.
- `bsbf-tlt-sw-link`:
  - To provide support for setting up and down VLAN interfaces when corresponding switch port comes up or down.
- `fping`:
  - To monitor network connectivity.
- `golang`:
  - Dependency for xray-core.
- `htop`:
  - To monitor the usage of system resources.
- `{hwdata,libevdev,libimobiledevice-glue,libimobiledevice,libplist,libtatsu,libudev-zero,libusbmuxd,usbutils}`:
  - Dependency for usbmuxd. On libevdev, compared to the OpenWrt packages feed, the meson include path is different.
- `plp-mtu-discovery`:
  - Refer to OpenWrt packages documentation below.
- `tcp-in-udp`:
  - Refer to OpenWrt packages documentation below. Compared to OpenWrt, the binary is provided pre-compiled as Teltonika SDK can't compile it.
- `usbmode`:
  - To switch the mode of cellular modems.
- `usbmuxd`:
  - To support iOS tethering.
- `xray-core`:
  - The proxy programme for the BSBF bonding solution. Compared to the OpenWrt packages feed, the golang include path is different.
- `zram-swap`:
  - To provide compressed swap memory.

Add bsbf_teltonika_resources feed to the bottom of `feeds.conf.default`:

```
src-git bsbf_teltonika_resources https://github.com/bondingshouldbefree/bsbf-teltonika-resources.git
```

Refresh feeds:

```
./scripts/feeds update
```

## [openwrt](https://github.com/bondingshouldbefree/openwrt)

This repository is a fork of OpenWrt, made for the sole purpose of submitting patches developed by the BondingShouldBeFree organisation.

The bsbf branch is used for compiling test images.

## [packages](https://github.com/bondingshouldbefree/packages)

This repository is a fork of OpenWrt's packages feed, made for the sole purpose of submitting patches developed by the BondingShouldBeFree organisation.

### bsbf-openwrt-resources

The package that contains the OpenWrt-specific resources for the BSBF bonding solution client.

- `files/etc/hotplug.d/net/99-bsbf-autoconf-dhcp`:
  - The hotplug script that creates a network with a DHCP client using a newly created network interface. It uses metric values from 1 to 8.
- `files/etc/hotplug.d/net/99-bsbf-autoconf-cellular`:
  - The hotplug script that creates a network with MBIM or QMI protocol using a newly created network interface. It uses metric values from 1 to 8.
- `files/etc/nftables.d/99-bsbf-bonding.nft`:
  - The nftables script to transparently proxy all traffic to xray-core.
- `files/etc/uci-defaults/99-bsbf-bonding`:
  - This script configures the system for the BSBF bonding solution client.

### bsbf-resources

The package that contains the remote and OpenWrt-specific resources for the BSBF bonding solution client.

- `files/etc/config/bsbf-mptcp`:
  - The UCI configuration file to declare a given interface(s) a subflow backup.
- `files/etc/hotplug.d/iface/99-bsbf-mptcp`:
  - The hotplug script that runs bsbf-mptcp-helper to remove the endpoint of an interface on ifdown. The implementation works around the current behaviour, which is the DEVICE variable not being reported on ifdown.
- `files/etc/hotplug.d/iface/99-bsbf-plpmtu`:
  - The hotplug script that runs bsbf-plpmtu when a matching network interface comes up.
- `files/etc/hotplug.d/iface/99-bsbf-tcp-in-udp`:
  - The hotplug script that runs bsbf-tcp-in-udp when a matching network interface comes up.
- `files/etc/init.d/bsbf-mptcp`:
  - The procd init script for bsbf-mptcp.
- `files/etc/init.d/bsbf-quectel-usbnet`:
  - The procd init script for bsbf-quectel-usbnet.
- `files/etc/init.d/bsbf-route`:
  - The procd init script for bsbf-route.

### plp-mtu-discovery

Perform Path MTU Discovery without relying on ICMP errors, which are often not delivered.

### tcp-in-udp

TCP-in-UDP is a lightweight TCP in UDP tunnel utilising eBPF.

## [plp-mtu-discovery](https://github.com/bondingshouldbefree/plp-mtu-discovery)

Perform Path MTU Discovery without relying on ICMP errors, which are often not delivered. This repository is a fork of the original to support an interface argument and provide a systemd service for the UDP server.

The use of this programme in the BSBF bonding solution:
- The server uses a PLPMTUD node to find the PMTU to the node. The PLPMTUD node must be able to handle 1500 MTU. Currently plpmtu.bondingshouldbefree.com:16383 is being used.
- The server runs the UDP server for its clients.
- The client uses its server as the PLPMTUD node to find the PMTU to the server.

## [rutos-sdk](https://github.com/bondingshouldbefree/rutos-sdk)

This repository contains the source code for certain Teltonika RUTOS SDKs with the BSBF bonding solution client.

- `{bsbf-compile-rutx-image.sh,bsbf-compile-rutm-image.sh}`:
  - This script compiles a Teltonika image with the BSBF bonding solution client.

## Notes

- The MPTCP implementation has got a logical limit of bonding a maximum of 8 paths.

- If you're seeing low download or upload throughput, lower the MTU of paths on the server and client more than the automatically found PMTU.

- On OpenWrt, metric values 1-100 are used for paths.

- Regarding matching packets for the TCP-in-UDP BPF programme, use fwmark for egress to prevent processing traffic that is not from the TCP programme. For client, this allows traffic to a different IP address with the same TCP port. For server, this prevents sending packet to BPF programme if the interface has multiple IP addresses assigned and if the TCP programme doesn't bind to all of them.

- Regarding iptables or nftables configuration, make packets from client destined to an IPv4 address that is local to the system bypass the proxy. This includes unicast DHCP Request packets with the IPv4 daddr not being a private IPv4 address, for which we had to put the 'udp dport 67' rule to have it bypass the proxy. This rule ensures that all traffic from client that is destined to the router bypasses the proxy. This covers the case that if the LAN IP address of the router is not a private IPv4 address, it wouldn't bypass the proxy.

  This makes it so that all packets that are supposed to be routed will be tproxied, except packets with a private IPv4 address as daddr.

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
  - Performance test:
    - `kmod-sched-flower kmod-veth coreutils-nproc iperf3`
