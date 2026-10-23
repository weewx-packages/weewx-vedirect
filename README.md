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

An existing WeeWX station is required. Replace `/path/to/weewx.conf`
with its configuration file before running the commands.

The catalog release requires Python >=3.10,<3.15 and WeeWX >=5.5,<6.

Before running the commands, activate the existing virtual environment that
runs your WeeWX service. Both `python` and `weectl` must come from that
environment. For an OS-packaged WeeWX installation, use compatible distribution
packages instead, or migrate WeeWX and its service to a virtual environment
first. Installing into an unrelated virtual environment will not supply the
running service with dependencies.

Install `pyserial` in the WeeWX environment:

```sh
python -m pip install 'pyserial==3.5'
```

The distribution is named `pyserial`; it provides the `serial` import.
Do not install the different package named `serial`.

Use the `weectl` from your WeeWX Python environment:

```sh
weectl extension install https://github.com/weewx-packages/catalog/releases/download/vedirect/v0.2.post1/vedirect-0.2.post1.tar.gz --config=/path/to/weewx.conf
weectl station reconfigure --driver=user.vedirect --config=/path/to/weewx.conf
```

Stock WeeWX 5 accepts this fixed URL or a downloaded copy of the same archive.
No catalog integration is required. Restart WeeWX yourself after configuration
changes.

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
to amps/volts; history values remain raw device counters. The module includes
the example schema `user.vedirect.schema`, but its `LOAD` and `ERR` columns
differ from the emitted `load` and `error` names. Match archive columns to the
emitted names and declare their observation units before storing or reporting
them. The installer does not select a schema or supply a skin.

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
