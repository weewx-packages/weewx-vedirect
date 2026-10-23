# Victron VE.Direct for WeeWX

Collect the serial text protocol from Victron VE.Direct devices, such as an MPPT
solar charge controller. The driver emits `I`, `V`, `VPV`, `PPV`, `CS`, `error`,
`load` and history counters `H19` through `H23` when present. It does not use
Victron's cloud, Bluetooth or VE.Can interfaces.

Copyright 2018-2022 Matthew Wall. Distributed under [GPLv3](license).

This repository mirrors upstream source. Install the separately repaired catalog
archive **0.2.post1** below for the maintained WeeWX 5 distribution.
A branch download does not contain those catalog repairs.

## Install

The catalog release requires Python >=3.10,<3.15 and WeeWX >=5.5,<6.

Install `pyserial` in the WeeWX environment:

```sh
python -m pip install pyserial
```

Use the `weectl` from your WeeWX Python environment:

```sh
weectl extension install https://github.com/weewx-packages/catalog/releases/download/vedirect/v0.2.post1/vedirect-0.2.post1.tar.gz --config=/path/to/weewx.conf
weectl station reconfigure --driver=user.vedirect --config=/path/to/weewx.conf
```

Replace `/path/to/weewx.conf` with your station configuration. Stock WeeWX 5
accepts this fixed URL or a downloaded copy of the same archive. No catalog
integration is required. Restart WeeWX yourself after configuration changes.

## Connect a device

Use a suitable VE.Direct serial interface and select its device path:

```ini
[Station]
    station_type = VEDirect
[VEDirect]
    driver = user.vedirect
    port = /dev/ttyUSB0
    poll_interval = 1
```

Give the WeeWX service user serial access. The driver opens at 19200 baud and
validates text-frame checksums. It converts `I`, `V` and `VPV` from milli-units
to amps/volts; history values remain raw device counters. This package includes
no archive schema or skin. Declare the corresponding observation units and
archive columns before using those fields in reports or storing history.

The mirror's Python 2 serial handling differs from the catalog archive. Do not
install the mirror branch over the repaired release. No device was tested during
this documentation check. Stop WeeWX before a standalone serial read; inspect
options without opening the port using:

```sh
PYTHONPATH=/path/to/station/bin python -m user.vedirect --help
```

Protocol notes and field definitions remain in [vedirect.py](bin/user/vedirect.py).
Upstream: [matthewwall/weewx-vedirect](https://github.com/matthewwall/weewx-vedirect).

<!-- Documentation updated 2026-10-23 by weewx-packages. -->
