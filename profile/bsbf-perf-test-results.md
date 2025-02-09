Results on a 12-thread amd64 CPU, clock speed of every thread at ~4370 MHz
whilst testing, all tests utilise 100% total CPU unless stated otherwise:

## bsbf-perf-test
```
u32:                    15.2 Gbits/sec
ranged-flower:          20.1 Gbits/sec
flower:                 19.0 Gbits/sec
no-matching:            15.7 Gbits/sec
disable-gso-gro:        22.0 Gbits/sec
default:                336 Gbits/sec
```

## bsbf-perf-proxy-test xray-tproxy
```
fw-u32:                 7.28 Gbits/sec
fw-flower:              7.24 Gbits/sec
u32-u32:                7.43 Gbits/sec
u32-flower:             6.99 Gbits/sec
no-matching:            7.26 Gbits/sec
disable-gso-gro:        8.42 Gbits/sec
default:                42.2 Gbits/sec
```

## bsbf-perf-proxy-test sing-box-tun
```
fw-u32:                 7.02 Gbits/sec
fw-flower:              6.83 Gbits/sec
u32-u32:                6.94 Gbits/sec
u32-flower:             6.71 Gbits/sec
no-matching:            6.92 Gbits/sec
disable-gso-gro:        8.38 Gbits/sec
default:                14.6 Gbits/sec (~45% total CPU utilisation)
```

## bsbf-perf-proxy-test sing-box-tproxy
```
fw-u32:                 6.69 Gbits/sec
fw-flower:              6.41 Gbits/sec
u32-u32:                6.65 Gbits/sec
u32-flower:             6.47 Gbits/sec
no-matching:            6.78 Gbits/sec
disable-gso-gro:        8.12 Gbits/sec
default:                27.7 Gbits/sec
```

## bsbf-perf-proxy-test v2ray-tproxy
```
fw-u32:                 6.64 Gbits/sec
fw-flower:              6.27 Gbits/sec
u32-u32:                6.48 Gbits/sec
u32-flower:             6.38 Gbits/sec
no-matching:            6.60 Gbits/sec
disable-gso-gro:        7.98 Gbits/sec
default:                28.7 Gbits/sec
```

Results on a 4-thread mips32 CPU, clock speed of every thread at 880 MHz whilst
testing, all tests utilise 100% total CPU unless stated otherwise:

## bsbf-perf-test
```
u32:                    294 Mbits/sec
ranged-flower:          464 Mbits/sec
flower:                 472 Mbits/sec
no-matching:            241 Mbits/sec
disable-gso-gro:        510 Mbits/sec
default:                1.71 Gbits/sec
```

## bsbf-perf-proxy-test sing-box-tun
```
fw-u32:                 66.6 Mbits/sec
fw-flower:              64.2 Mbits/sec
u32-u32:                67.3 Mbits/sec
u32-flower:             62.7 Mbits/sec
no-matching:            68.9 Mbits/sec
disable-gso-gro:        89.8 Mbits/sec
default:                147 Mbits/sec (~90% total CPU utilisation)
```

## bsbf-perf-proxy-test sing-box-tproxy
```
fw-u32:                 77.1 Mbits/sec
fw-flower:              76.1 Mbits/sec
u32-u32:                80.5 Mbits/sec
u32-flower:             73.6 Mbits/sec
no-matching:            76.9 Mbits/sec
disable-gso-gro:        112 Mbits/sec
default:                280 Mbits/sec
```

Results on a 4-thread arm64 CPU, clock speed of every thread at 1500 MHz whilst
testing, all tests utilise 100% total CPU unless stated otherwise:

## bsbf-perf-test
```
u32:                    3.31 Gbits/sec
ranged-flower:          4.71 Gbits/sec
flower:                 4.76 Gbits/sec
no-matching:            3.41 Gbits/sec
disable-gso-gro:        5.09 Gbits/sec
default:                27.9 Gbits/sec
```

## bsbf-perf-proxy-test sing-box-tun
```
fw-u32:                 1.14 Gbits/sec
fw-flower:              1.08 Gbits/sec
u32-u32:                1.13 Gbits/sec
u32-flower:             1.10 Gbits/sec
no-matching:            1.19 Gbits/sec
disable-gso-gro:        1.54 Gbits/sec
default:                2.72 Gbits/sec (~90% total CPU utilisation)
```

## bsbf-perf-proxy-test sing-box-tproxy
```
fw-u32:                 1.05 Gbits/sec
fw-flower:              1.01 Gbits/sec
u32-u32:                1.05 Gbits/sec
u32-flower:             1.01 Gbits/sec
no-matching:            1.07 Gbits/sec
disable-gso-gro:        1.40 Gbits/sec
default:                5.02 Gbits/sec
```

Results on a 1-thread mips32 CPU, clock speed of every thread at 1100 MHz whilst
testing, all tests utilise 100% total CPU unless stated otherwise:

## bsbf-perf-proxy-test xray-tproxy
```
fw-u32:                 45.5 Mbits/sec
fw-flower:              39.9 Mbits/sec
u32-u32:                45.8 Mbits/sec
u32-flower:             39.7 Mbits/sec
no-matching:            46.1 Mbits/sec
disable-gso-gro:        56.8 Mbits/sec
default:                121 Mbits/sec
```

## bsbf-perf-proxy-test sing-box-tun
```
fw-u32:                 38.9 Mbits/sec
fw-flower:              36.8 Mbits/sec
u32-u32:                38.8 Mbits/sec
u32-flower:             36.9 Mbits/sec
no-matching:            40.5 Mbits/sec
disable-gso-gro:        51.0 Mbits/sec
default:                80.0 Mbits/sec
```

## bsbf-perf-proxy-test sing-box-tproxy
```
fw-u32:                 48.5 Mbits/sec
fw-flower:              45.3 Mbits/sec
u32-u32:                50.2 Mbits/sec
u32-flower:             45.2 Mbits/sec
no-matching:            49.4 Mbits/sec
disable-gso-gro:        66.4 Mbits/sec
default:                129 Mbits/sec
```

## bsbf-perf-proxy-test v2ray-tproxy
```
fw-u32:                 11.5 Mbits/sec
fw-flower:              11.3 Mbits/sec
u32-u32:                11.6 Mbits/sec
u32-flower:             11.1 Mbits/sec
no-matching:            12.1 Mbits/sec
disable-gso-gro:        13.5 Mbits/sec
default:                16.1 Mbits/sec
```
