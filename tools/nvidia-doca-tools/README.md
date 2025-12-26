# NVIDIA DOCA Tools Extension

This extension provides management and diagnostic tools for NVIDIA BlueField DPUs and ConnectX NICs.

## What's Included

### Firmware Management
- **mstflint** - Firmware burning and management tool
- **mstconfig** - Device configuration tool
- **mstfwmanager** - Firmware update manager
- **mstfwreset** - Firmware reset utility

### Diagnostic Tools
- **mstregdump** - Register dump utility
- **mstlink** - Link status and configuration
- **mstmcra** - Memory access tool
- **mstmread** - Memory read utility
- **mstmwrite** - Memory write utility

### Performance Testing
- **perftest** - RDMA performance testing suite
  - ib_send_bw - Send bandwidth test
  - ib_send_lat - Send latency test
  - ib_read_bw - Read bandwidth test
  - ib_read_lat - Read latency test
  - ib_write_bw - Write bandwidth test
  - ib_write_lat - Write latency test

### Network Tools
- **mlnx-ethtool** - Extended ethtool for NVIDIA NICs
- **mlnx-iproute2** - Enhanced iproute2 utilities
- **mlnx-tools** - NVIDIA networking utilities

### Management Scripts
- **ofed-scripts** - OFED management and configuration scripts

## Supported Hardware

- **BlueField DPUs:** BlueField-3, BlueField-2
- **ConnectX NICs:** ConnectX-8, ConnectX-7, ConnectX-6 DX/LX, ConnectX-5, ConnectX-4 LX

## Installation

```yaml
machine:
  install:
    extensions:
      - image: ghcr.io/siderolabs/nvidia-doca-tools:VERSION
```

## When to Use This Extension

Use `nvidia-doca-tools` when you need to:
- Update firmware on NVIDIA NICs/DPUs
- Diagnose hardware issues
- Configure device settings
- Test RDMA performance
- Monitor device health
- Manage device configuration

**Note:** This extension can be installed independently or alongside DOCA driver extensions.

## Common Use Cases

### Firmware Update

```bash
# Check current firmware version
mstflint -d /dev/mst/mt4125_pciconf0 query

# Update firmware
doca-fw-update -d /dev/mst/mt4125_pciconf0 -i fw-ConnectX7-rel.bin burn

# Reset after firmware update
mstfwreset -d /dev/mst/mt4125_pciconf0 reset
```

### Device Configuration

```bash
# Query device configuration
mstconfig -d /dev/mst/mt4125_pciconf0 query

# Set SR-IOV
mstconfig -d /dev/mst/mt4125_pciconf0 set SRIOV_EN=1 NUM_OF_VFS=8

# Enable RoCE
mstconfig -d /dev/mst/mt4125_pciconf0 set ROCE_EN=1
```

### Performance Testing

```bash
# Bandwidth test (server side)
ib_send_bw -d mlx5_0 -i 1

# Bandwidth test (client side)
ib_send_bw -d mlx5_0 -i 1 <server-ip>

# Latency test
ib_send_lat -d mlx5_0 -i 1 <server-ip>

# All-to-all test
ib_send_bw -d mlx5_0 -i 1 -a
```

### Link Status

```bash
# Check link status
mstlink -d /dev/mst/mt4125_pciconf0

# Show detailed link information
mlnx-ethtool eth0
```

### Diagnostics

```bash
# Dump device registers
mstregdump /dev/mst/mt4125_pciconf0

# Check device health
mlnx-tools health-check

# Show device information
mstflint -d /dev/mst/mt4125_pciconf0 query full
```

## Device Discovery

```bash
# List MST devices
ls /dev/mst/

# If devices not found, start MST service
mst start

# Show all devices
mst status -v
```

## Compatibility with DOCA Extensions

This tools extension works with any DOCA driver extension:

```yaml
machine:
  install:
    extensions:
      # Driver extension (choose one)
      - image: ghcr.io/siderolabs/nvidia-doca-networking:VERSION
      # Tools extension
      - image: ghcr.io/siderolabs/nvidia-doca-tools:VERSION
```

## Safety Notes

⚠️ **Firmware Updates:**
- Always backup current firmware before updating
- Ensure power stability during firmware updates
- Verify firmware compatibility with your hardware
- Follow NVIDIA's firmware update procedures

⚠️ **Configuration Changes:**
- Some configuration changes require a reboot
- Document current settings before making changes
- Test configuration changes in non-production first

## Troubleshooting

### MST Device Not Found

```bash
# Start MST service
mst start

# Cable/restart MST
mst restart

# Check if devices are detected
mst status
```

### Permission Denied

Tools require access to device files. Ensure proper permissions or run with elevated privileges.

### Firmware Update Failed

```bash
# Check device status
mstflint -d /dev/mst/mt4125_pciconf0 query

# Try force burn (use with caution)
mstflint -d /dev/mst/mt4125_pciconf0 -i fw.bin -allow_psid_change burn
```

## Resources

- [NVIDIA MFT User Manual](https://docs.nvidia.com/networking/display/mftv4latest)
- [NVIDIA Firmware Tools](https://network.nvidia.com/support/firmware/firmware-downloads/)
- [DOCA Tools Documentation](https://docs.nvidia.com/doca/sdk/)
- [Performance Testing Guide](https://docs.nvidia.com/doca/sdk/)
