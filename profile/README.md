<picture>
  <source srcset="include/logolight.png" media="(prefers-color-scheme: dark)">
  <source srcset="include/logodark.png" media="(prefers-color-scheme: light)">
  <img src="include/logolight.png" alt="BSBF Logo">
</picture>

BondingShouldBeFree bonding solution based on Multipath TCP (MPTCP) is a set of programmes that utilises MPTCP, providing advanced network aggregation and redundancy capabilities.

This project leverages an upstream kernel and is currently focused on supporting conventional Linux distributions, all [OpenWrt](https://openwrt.org/)-supported devices, and [Teltonika](https://wiki.teltonika-networks.com/view/Main_Page) RUTX and RUTM series routers.

The main goal of BSBF bonding is to combine multiple internet connections into a single connection to improve both bandwidth throughput and network reliability. By aggregating multiple internet breakouts, users can achieve better performance and maintain connectivity as long as there is one working connection.

## Client Installation

### Conventional Linux Distributions

Replace the server IPv4 address, server port, and UUID with yours below. Then run the command below.

```
curl -fsSL https://github.com/bondingshouldbefree/bsbf-client-installer/raw/main/bsbf-client-installer.sh \
  | sudo sh -s -- --server-ipv4 25.0.0.1 --server-port 32768 --uuid 60d210ef-7271-4dc9-9b93-01563608bf90
```

### OpenWrt

Head to [the BondingShouldBeFree firmware selector](https://fs.bondingshouldbefree.com/). Put in your OpenWrt-supported device model. Put in your server IPv4 address, server port, and UUID. Then download the generated image for your device.

In the future, there will be an option to install the solution by running a command.

### Teltonika

For now, you need to compile an image for your device. Replace the server IPv4 address, server port, and UUID with yours below. Then run the command below.

For RUTX routers:

```
git clone https://github.com/bondingshouldbefree/rutos-sdk.git -b RUTX
cd rutos-sdk
./bsbf-compile-rutx-image.sh --server-ipv4 25.0.0.1 --server-port 32768 --uuid 60d210ef-7271-4dc9-9b93-01563608bf90
```

The image file will be at `bin/targets/ipq40xx/generic/tltFws/`.

For RUTM routers:
```
git clone https://github.com/bondingshouldbefree/rutos-sdk.git -b RUTM
cd rutos-sdk
./bsbf-compile-rutm-image.sh --server-ipv4 25.0.0.1 --server-port 32768 --uuid 60d210ef-7271-4dc9-9b93-01563608bf90
```

The image file will be at `bin/targets/ramips/mt7621/tltFws/`.

In the future, there may be an option to install the solution by running a command.

## Server Installation and Management Guide

Run the command below.

```
curl -fsSL https://github.com/bondingshouldbefree/bsbf-server-installer/raw/main/bsbf-server-installer.sh | sudo sh
```

Add a client with 50 Mbps download and upload limit. The command will return the UUID and server port:

```
$ sudo bsbf-add-client 50
60d210ef-7271-4dc9-9b93-01563608bf90 32768
```

Remove a client with a given UUID:

```
$ sudo bsbf-remove-client 60d210ef-7271-4dc9-9b93-01563608bf90
```

## Tested OpenWrt Devices

Head [here](tested-openwrt-devices.md).

## Project Documentation

Head [here](documentation.md).

## Support Community

* [Discord](https://discord.gg/qJ7KP6SQxC): For usage, projects, discussions and hardware advice.
