<!--startmeta
custom_edit_url: "https://github.com/netdata/netdata/edit/master/src/go/plugin/go.d/collector/ethtool/README.md"
meta_yaml: "https://github.com/netdata/netdata/edit/master/src/go/plugin/go.d/collector/ethtool/metadata.yaml"
sidebar_label: "Optical modules"
learn_status: "Published"
learn_rel_path: "Collecting Metrics/Networking Stack and Network Interfaces"
most_popular: False
message: "DO NOT EDIT THIS FILE DIRECTLY, IT IS GENERATED BY THE COLLECTOR'S metadata.yaml FILE"
endmeta-->

# Optical modules


<img src="https://netdata.cloud/img/network-wired.svg" width="150"/>


Plugin: go.d.plugin
Module: ethtool

<img src="https://img.shields.io/badge/maintained%20by-Netdata-%2300ab44" />

## Overview

This collector monitors optical transceiver modules' diagnostic parameters  (temperature, voltage, laser bias current, transmit/receive power levels) from network interfaces  equipped with modules that support Digital Diagnostic Monitoring (DDM). It relies on the [`ethtool`](https://man7.org/linux/man-pages/man8/ethtool.8.html) CLI tool but avoids directly executing the binary. Instead, it utilizes `ndsudo`, a Netdata helper specifically designed to run privileged commands securely within the Netdata environment. This approach eliminates the need to use `sudo`, improving security and potentially simplifying permission management.




This collector is only supported on the following platforms:

- Linux

This collector only supports collecting metrics from a single instance of this integration.


### Default Behavior

#### Auto-Detection

This integration doesn't support auto-detection.

#### Limits

The default configuration for this integration does not impose any limits on data collection.

#### Performance Impact

The default configuration for this integration is not expected to impose a significant performance impact on the system.


## Metrics

Metrics grouped by *scope*.

The scope defines the instance that the metric belongs to. An instance is uniquely identified by a set of labels.



### Per Optical Transceiver Module

Metrics collected from optical transceiver modules that support Digital Diagnostic Monitoring (DDM).

Labels:

| Label      | Description     |
|:-----------|:----------------|
| iface | Network interface name where the optical transceiver module is installed. |

Metrics:

| Metric | Dimensions | Unit |
|:------|:----------|:----|
| ethtool.optical_module_receiver_signal_power | rx_power | dBm |
| ethtool.optical_module_laser_output_power | tx_power | dBm |
| ethtool.optical_module_laser_bias_current | bias_current | mA |
| ethtool.optical_module_temperature | temperature | Celsius |
| ethtool.optical_module_voltage | voltage | Volts |



## Alerts

There are no alerts configured by default for this integration.


## Setup

### Prerequisites

No action required.

### Configuration

#### File

The configuration file name for this integration is `go.d/ethtool.conf`.


You can edit the configuration file using the [`edit-config`](https://github.com/netdata/netdata/blob/master/docs/netdata-agent/configuration/README.md#edit-a-configuration-file-using-edit-config) script from the
Netdata [config directory](https://github.com/netdata/netdata/blob/master/docs/netdata-agent/configuration/README.md#the-netdata-config-directory).

```bash
cd /etc/netdata 2>/dev/null || cd /opt/netdata/etc/netdata
sudo ./edit-config go.d/ethtool.conf
```
#### Options

The following options can be defined globally: update_every.


<details open><summary>Config options</summary>

| Name | Description | Default | Required |
|:----|:-----------|:-------|:--------:|
| update_every | Data collection frequency. | 10 | no |
| timeout | Timeout for executing the binary, specified in seconds. | 2 | no |
| optical_interfaces | Space-separated list of optical interface names which must have optical transceiver modules with [DDM](https://en.wikipedia.org/wiki/Small_Form-factor_Pluggable#Digital_diagnostics_monitoring). |  | yes |

</details>

#### Examples

##### Custom binary path

The executable is not in the directories specified in the PATH environment variable.

<details open><summary>Config</summary>

```yaml
jobs:
  - name: ethtool
    binary_path: /usr/local/sbin/ethtool
    optical_interfaces: "enp1s0 enp1s1 enp2s0"

```
</details>



## Troubleshooting

### Debug Mode

**Important**: Debug mode is not supported for data collection jobs created via the UI using the Dyncfg feature.

To troubleshoot issues with the `ethtool` collector, run the `go.d.plugin` with the debug option enabled. The output
should give you clues as to why the collector isn't working.

- Navigate to the `plugins.d` directory, usually at `/usr/libexec/netdata/plugins.d/`. If that's not the case on
  your system, open `netdata.conf` and look for the `plugins` setting under `[directories]`.

  ```bash
  cd /usr/libexec/netdata/plugins.d/
  ```

- Switch to the `netdata` user.

  ```bash
  sudo -u netdata -s
  ```

- Run the `go.d.plugin` to debug the collector:

  ```bash
  ./go.d.plugin -d -m ethtool
  ```

### Getting Logs

If you're encountering problems with the `ethtool` collector, follow these steps to retrieve logs and identify potential issues:

- **Run the command** specific to your system (systemd, non-systemd, or Docker container).
- **Examine the output** for any warnings or error messages that might indicate issues.  These messages should provide clues about the root cause of the problem.

#### System with systemd

Use the following command to view logs generated since the last Netdata service restart:

```bash
journalctl _SYSTEMD_INVOCATION_ID="$(systemctl show --value --property=InvocationID netdata)" --namespace=netdata --grep ethtool
```

#### System without systemd

Locate the collector log file, typically at `/var/log/netdata/collector.log`, and use `grep` to filter for collector's name:

```bash
grep ethtool /var/log/netdata/collector.log
```

**Note**: This method shows logs from all restarts. Focus on the **latest entries** for troubleshooting current issues.

#### Docker Container

If your Netdata runs in a Docker container named "netdata" (replace if different), use this command:

```bash
docker logs netdata 2>&1 | grep ethtool
```

