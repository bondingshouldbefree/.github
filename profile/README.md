<picture>
  <source srcset="include/logolight.png" media="(prefers-color-scheme: dark)">
  <source srcset="include/logodark.png" media="(prefers-color-scheme: light)">
  <img src="include/logolight.png" alt="BSBF Logo">
</picture>

BondingShouldBeFree bonding solution based on Multipath TCP (MPTCP) is a set of programmes that utilises MPTCP, providing advanced network aggregation and redundancy capabilities.

This project leverages an upstream kernel and is currently focused on supporting conventional Linux distributions, all [OpenWrt](https://openwrt.org/)-supported devices, and [Teltonika](https://wiki.teltonika-networks.com/view/Main_Page) RUTX and RUTM series routers.

The main goal of BSBF bonding is to combine multiple internet connections into a single connection to improve both bandwidth throughput and network reliability. By aggregating multiple internet breakouts, users can achieve better performance and maintain connectivity as long as there is one working connection.

## Project Documentation

Head [here](documentation.md).

## Tested OpenWrt Devices

Head [here](tested-openwrt-devices.md).

## Support Community

* [Discord](https://discord.gg/7PQYdWT69x): For usage, projects, discussions and hardware advice.

## Client Installation

### Conventional Linux Distributions

Replace the server IPv4 address, server port, and UUID with yours below. Then run the command below. This command can be re-run to change the server and/or upgrade the solution.

```
curl -fsSL https://github.com/bondingshouldbefree/bsbf-resources/raw/main/resources-client/bsbf-client-installer.sh \
  | sudo sh -s -- --server-ipv4 25.0.0.1 --server-port 16384 --uuid 60d210ef-7271-4dc9-9b93-01563608bf90
```

To uninstall, run the command below.

```
curl -fsSL https://github.com/bondingshouldbefree/bsbf-resources/raw/main/resources-client/bsbf-client-installer.sh \
  | sudo sh -s -- --uninstall
```

### OpenWrt

Replace the server IPv4 address, server port, and UUID with yours below. Then run the command below. This command can be re-run to change the server and/or upgrade the solution.

```
apk add curl && curl -fsSL https://github.com/bondingshouldbefree/bsbf-resources/raw/main/resources-client/bsbf-client-openwrt-installer.sh \
  | sh -s -- --server-ipv4 25.0.0.1 --server-port 16384 --uuid 60d210ef-7271-4dc9-9b93-01563608bf90
```

If the device runs out of storage at install, you can try to build an image instead by following the steps below.

Head to [the BondingShouldBeFree firmware selector](https://fs.bondingshouldbefree.com/). Put in your OpenWrt-supported device model. Put in your server IPv4 address, server port, and UUID. Then download the generated image for your device.

The network interface with the biggest number at the end, such as lan5, will be made LAN. If there are no interfaces with a number at the end, the first found LAN network interface will be used instead. Other interfaces will be made WAN.

To uninstall, run the command below. This will not revert the changes made to the network configuration.

```
curl -fsSL https://github.com/bondingshouldbefree/bsbf-resources/raw/main/resources-client/bsbf-client-openwrt-installer.sh \
  | sh -s -- --uninstall
```

### Teltonika RUTOS

For now, you need to compile an image for your device. Replace the server IPv4 address, server port, and UUID with yours below. Then run the command below.

For RUTX routers:

```
git clone https://github.com/bondingshouldbefree/rutos-sdk.git -b rutx
cd rutos-sdk
./bsbf-compile-rutx-image.sh --server-ipv4 25.0.0.1 --server-port 16384 --uuid 60d210ef-7271-4dc9-9b93-01563608bf90
```

The image file will be at `bin/targets/ipq40xx/generic/tltFws/`.

For RUTM routers:
```
git clone https://github.com/bondingshouldbefree/rutos-sdk.git -b rutm
cd rutos-sdk
./bsbf-compile-rutm-image.sh --server-ipv4 25.0.0.1 --server-port 16384 --uuid 60d210ef-7271-4dc9-9b93-01563608bf90
```

The image file will be at `bin/targets/ramips/mt7621/tltFws/`.

In the future, there may be an option to install the solution by running a command.

## Server Installation and Management Guide

Run the command below. This command can be re-run to upgrade the solution.

```
curl -fsSL https://github.com/bondingshouldbefree/bsbf-resources/raw/main/resources-server/bsbf-server-installer.sh | sudo sh
```

Add a client with 50 Mbps download and upload limit. To add one without download or upload limit, specify 0 instead. The command will return the UUID and server port:

```
sudo bsbf-add-client 50
16384 60d210ef-7271-4dc9-9b93-01563608bf90
```

Remove a client with a given port, port range, or UUID:

```
sudo bsbf-remove-client 16384
sudo bsbf-remove-client 16384-16400
sudo bsbf-remove-client 60d210ef-7271-4dc9-9b93-01563608bf90
```

To uninstall, run the command below.

```
curl -fsSL https://github.com/bondingshouldbefree/bsbf-resources/raw/main/resources-server/bsbf-server-installer.sh \
  | sudo sh -s -- --uninstall
```
