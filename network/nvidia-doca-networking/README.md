# NVIDIA DOCA Networking Extension

This extension provides NVIDIA DOCA networking acceleration for BlueField DPUs and ConnectX NICs.

## Profile: doca-networking (RECOMMENDED)

This is the recommended installation for accelerated networking workloads without the full DOCA SDK overhead.

## What's Included

- **MLNX_OFED Drivers:**
  - All kernel modules from doca-ofed
  - Full RDMA/InfiniBand support

- **DOCA Core:**
  - Device abstraction and management
  - Memory management (zero-copy buffers)
  - Progress engine for async operations

- **MLNX-DPDK:**
  - Data Plane Development Kit
  - High-performance packet processing

- **OVS-DOCA:**
  - Open vSwitch with DOCA acceleration
  - Hardware-offloaded switching

- **DOCA Flow:**
  - Packet processing and flow control
  - Hardware flow steering
  - Connection tracking

## Supported Hardware

- **BlueField DPUs:** BlueField-3, BlueField-2
- **ConnectX NICs:** ConnectX-8, ConnectX-7, ConnectX-6 DX/LX, ConnectX-5, ConnectX-4 LX

## Installation

```yaml
machine:
  install:
    extensions:
      - image: ghcr.io/siderolabs/nvidia-doca-networking:VERSION
```

## When to Use This Extension

Use `nvidia-doca-networking` when you:
- Need accelerated networking performance
- Want hardware offload for packet processing
- Use DPDK-based applications
- Need OVS with hardware acceleration
- Require flow steering and connection tracking
- Want DOCA features without the full SDK

## Use Cases

- High-performance networking applications
- Software-defined networking (SDN)
- Network function virtualization (NFV)
- Container networking with hardware acceleration
- RDMA workloads with DOCA enhancements

## Configuration Example

### Enable RDMA

```yaml
machine:
  network:
    interfaces:
      - interface: eth0
        mtu: 9000
```

### Load Required Modules

```yaml
machine:
  kernel:
    modules:
      - name: mlx5_core
      - name: mlx5_ib
      - name: ib_uverbs
```

## Alternative Extensions

- **nvidia-doca-ofed** - Minimal driver-only installation
- **nvidia-doca-roce** - Lightweight RoCE-only
- **nvidia-doca-all** - Full DOCA SDK with all libraries
- **nvidia-doca-tools** - Management and diagnostic tools

## Verification

```bash
# Check DOCA version
doca-version

# Check loaded modules
lsmod | grep mlx

# List RDMA devices
rdma link

# Check DPDK devices
dpdk-devbind.py --status

# Verify DOCA Flow
doca_flow_query
```

## Performance Tuning

For optimal performance:

```yaml
machine:
  sysctls:
    net.core.rmem_max: 268435456
    net.core.wmem_max: 268435456
    net.ipv4.tcp_rmem: "4096 87380 134217728"
    net.ipv4.tcp_wmem: "4096 65536 134217728"
```

## Resources

- [NVIDIA DOCA Documentation](https://docs.nvidia.com/doca/sdk/)
- [DOCA Networking Profile](https://docs.nvidia.com/doca/sdk/DOCA-Profiles/)
- [DOCA Flow Guide](https://docs.nvidia.com/doca/sdk/DOCA-Flow/)
- [MLNX-DPDK Documentation](https://docs.nvidia.com/doca/sdk/)
