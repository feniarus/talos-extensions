# Power Monitoring Extension

This extension provides CPU power monitoring capabilities for Intel and AMD processors on Talos Linux.

## Overview

The power-monitoring extension enables real-time monitoring of CPU power consumption and energy usage through kernel interfaces. This is essential for:

- Energy efficiency monitoring
- Power consumption tracking
- Carbon footprint calculation
- Thermal management
- Power capping and optimization
- Kubernetes resource monitoring

## What's Included

### Intel RAPL (Running Average Power Limit)

**Kernel Modules:**
- `intel_rapl_common` - Core RAPL functionality
- `intel_rapl_msr` - MSR-based RAPL interface
- `rapl` - Power capping framework

**Monitoring Capabilities:**
- Package power (whole CPU socket)
- Core power (CPU cores)
- Uncore power (integrated GPU, memory controller)
- DRAM power (memory subsystem)
- PSys power (platform/system level)

### AMD Energy Monitoring

**Kernel Modules:**
- `amd_energy` - AMD processor energy monitoring

**Monitoring Capabilities:**
- Per-socket energy counters
- Per-core energy counters

### Supporting Modules

- `msr` - Model-Specific Register access for direct CPU register reads
- `powercap` - Power capping framework core

## Supported Hardware

### Intel Processors
- Intel Sandy Bridge (2nd Gen Core) and newer
- Intel Xeon E3/E5/E7 v2 and newer
- Intel Xeon Scalable (Skylake-SP and newer)

### AMD Processors
- AMD Family 17h (Zen, Zen+, Zen 2)
- AMD Family 19h (Zen 3, Zen 4)
- AMD EPYC (Naples, Rome, Milan, Genoa)

## Installation

```yaml
machine:
  install:
    extensions:
      - image: ghcr.io/siderolabs/power-monitoring:VERSION
```

## Usage

### Accessing Power Data

Once the extension is loaded, power monitoring data is exposed via sysfs:

#### Intel RAPL Interface

```bash
# List available power zones
ls /sys/class/powercap/intel-rapl/

# Read package energy (in microjoules)
cat /sys/class/powercap/intel-rapl/intel-rapl:0/energy_uj

# Read core energy
cat /sys/class/powercap/intel-rapl/intel-rapl:0/intel-rapl:0:0/energy_uj

# Read DRAM energy
cat /sys/class/powercap/intel-rapl/intel-rapl:0/intel-rapl:0:1/energy_uj
```

#### AMD Energy Interface

```bash
# AMD energy is exposed via hwmon
ls /sys/class/hwmon/

# Find AMD energy device
grep -l amd_energy /sys/class/hwmon/hwmon*/name

# Read energy (in microjoules)
cat /sys/class/hwmon/hwmon*/energy*_input
```

### Calculating Power Consumption

Power (Watts) = ΔEnergy (Joules) / ΔTime (Seconds)

Example script:
```bash
#!/bin/bash
ENERGY_FILE="/sys/class/powercap/intel-rapl/intel-rapl:0/energy_uj"

# Read initial energy
E1=$(cat $ENERGY_FILE)
sleep 1
# Read energy after 1 second
E2=$(cat $ENERGY_FILE)

# Calculate power in Watts
POWER=$(echo "scale=2; ($E2 - $E1) / 1000000" | bc)
echo "Power consumption: ${POWER} W"
```

## Integration with Monitoring Systems

### Prometheus Node Exporter

The node_exporter includes a RAPL collector that can be enabled:

```yaml
# Enable RAPL collector
--collector.rapl
```

Metrics exposed:
```
node_rapl_package_joules_total
node_rapl_core_joules_total
node_rapl_dram_joules_total
node_rapl_uncore_joules_total
```

### Custom Exporters

Example Prometheus exporter in Python:

```python
from prometheus_client import Gauge, start_http_server
import time

# Create metrics
package_power = Gauge('cpu_package_power_watts', 'CPU package power consumption')

def read_energy():
    with open('/sys/class/powercap/intel-rapl/intel-rapl:0/energy_uj', 'r') as f:
        return int(f.read())

def calculate_power():
    e1 = read_energy()
    time.sleep(1)
    e2 = read_energy()
    return (e2 - e1) / 1_000_000  # Convert to Watts

if __name__ == '__main__':
    start_http_server(8000)
    while True:
        package_power.set(calculate_power())
        time.sleep(5)
```

### Kubernetes Integration

Deploy as a DaemonSet to collect power metrics from all nodes:

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: power-exporter
spec:
  selector:
    matchLabels:
      app: power-exporter
  template:
    metadata:
      labels:
        app: power-exporter
    spec:
      hostNetwork: true
      containers:
      - name: exporter
        image: your-power-exporter:latest
        securityContext:
          privileged: true
        volumeMounts:
        - name: powercap
          mountPath: /sys/class/powercap
          readOnly: true
      volumes:
      - name: powercap
        hostPath:
          path: /sys/class/powercap
```

## Power Capping

Intel RAPL also supports power capping (limiting maximum power consumption):

```bash
# Set power limit to 100W (100000000 microwatts)
echo 100000000 > /sys/class/powercap/intel-rapl/intel-rapl:0/constraint_0_power_limit_uw

# Set time window to 1 second (1000000 microseconds)
echo 1000000 > /sys/class/powercap/intel-rapl/intel-rapl:0/constraint_0_time_window_us
```

**Note:** Power capping requires write permissions and may require additional configuration.

## Verification

### Check Loaded Modules

```bash
# Intel RAPL
lsmod | grep rapl

# AMD Energy
lsmod | grep amd_energy

# MSR
lsmod | grep msr
```

### Verify Sysfs Interface

```bash
# Intel
ls -la /sys/class/powercap/intel-rapl/

# AMD
ls -la /sys/class/hwmon/ | grep amd
```

### Test Reading

```bash
# Intel - should return a number (microjoules)
cat /sys/class/powercap/intel-rapl/intel-rapl:0/energy_uj

# AMD - should return energy values
cat /sys/class/hwmon/hwmon*/energy*_input
```

## Troubleshooting

### No Data Available

**Intel:**
```bash
# Check if CPU supports RAPL
dmesg | grep -i rapl

# Verify MSR module is loaded
lsmod | grep msr

# Check for RAPL zones
ls /sys/class/powercap/
```

**AMD:**
```bash
# Check if CPU supports energy monitoring
dmesg | grep -i amd_energy

# Verify hwmon devices
ls /sys/class/hwmon/
```

### Permission Denied

The extension configures read-only access by default. For power capping or write operations, additional permissions may be required.

### Counter Overflow

Energy counters are cumulative and may overflow. Always calculate differences between readings rather than using absolute values.

## Use Cases

### 1. Energy Efficiency Monitoring

Track power consumption trends over time to identify inefficient workloads or optimize scheduling.

### 2. Carbon Footprint Tracking

Calculate CO2 emissions based on power consumption and grid carbon intensity.

### 3. Cost Optimization

Monitor power usage to optimize cloud costs or data center expenses.

### 4. Thermal Management

Correlate power consumption with temperature to prevent thermal throttling.

### 5. Power-Aware Scheduling

Use power metrics to make intelligent scheduling decisions in Kubernetes.

### 6. SLA Compliance

Ensure workloads stay within power budgets defined in service level agreements.

## Limitations

- **Sampling Rate:** Energy counters update at hardware-defined intervals (typically milliseconds)
- **Accuracy:** RAPL provides estimates, not precise measurements
- **Granularity:** Per-core data may not be available on all processors
- **Overflow:** Counters wrap around after reaching maximum value
- **Platform Support:** Some features may not be available on all CPU models

## Resources

- [Intel RAPL Documentation](https://www.kernel.org/doc/html/latest/power/powercap/powercap.html)
- [AMD Energy Driver](https://www.kernel.org/doc/html/latest/hwmon/amd_energy.html)
- [Power Capping Framework](https://www.kernel.org/doc/html/latest/power/powercap/powercap.html)
- [Linux Hardware Monitoring](https://www.kernel.org/doc/html/latest/hwmon/index.html)

## Related Extensions

- **nut-client** - Network UPS Tools for power event handling
- **hardware-monitoring** (future) - Comprehensive hardware sensor monitoring

## Support

For issues or questions:
- Check kernel logs: `dmesg | grep -i "rapl\|amd_energy"`
- Verify CPU support: Check CPU specifications for RAPL/energy monitoring support
- Report issues: Open an issue in the extensions repository
