# NVIDIA DOCA RoCE Extension

This extension provides lightweight RDMA over Converged Ethernet (RoCE) support for NVIDIA ConnectX NICs.

## Profile: doca-roce

Minimal installation for RoCE-only workloads without the full DOCA stack.

## What's Included

- **Kernel Modules:**
  - rdma-core - RDMA userspace libraries
  - mlnx-ofa_kernel - OFED kernel modules

- **Tools:**
  - perftest - RDMA performance testing (ib_send_bw, ib_read_lat, etc.)
  - ofed-scripts - OFED management scripts
  - mlnx-tools - Basic NVIDIA networking tools

## Supported Hardware

- **ConnectX NICs:** ConnectX-8, ConnectX-7, ConnectX-6 DX/LX, ConnectX-5, ConnectX-4 LX
- **BlueField DPUs:** BlueField-3, BlueField-2 (RoCE mode)

## Installation

```yaml
machine:
  install:
    extensions:
      - image: ghcr.io/siderolabs/nvidia-doca-roce:VERSION
```

## When to Use This Extension

Use `nvidia-doca-roce` when you:
- Only need RoCE (RDMA over Ethernet) functionality
- Want the smallest installation footprint
- Don't require DOCA SDK features
- Run RDMA applications over Ethernet
- Need basic RDMA performance testing

## Use Cases

- Storage systems using RDMA (NVMe-oF, iSER)
- High-performance computing (HPC) over Ethernet
- Low-latency messaging systems
- RDMA-based distributed databases

## Configuration

### Enable RoCE

```yaml
machine:
  kernel:
    modules:
      - name: mlx5_core
      - name: mlx5_ib
      - name: ib_uverbs
      - name: rdma_cm
```

### Network Configuration

```yaml
machine:
  network:
    interfaces:
      - interface: eth0
        mtu: 9000  # Jumbo frames recommended for RDMA
```

### Enable RoCE v2 (Recommended)

RoCE v2 uses UDP/IP and is routable, unlike RoCE v1.

```yaml
machine:
  sysctls:
    net.ipv4.conf.all.rp_filter: 0
    net.ipv4.conf.default.rp_filter: 0
```

## Alternative Extensions

- **nvidia-doca-ofed** - Full MLNX_OFED drivers (includes RoCE + InfiniBand)
- **nvidia-doca-networking** - Accelerated networking with DOCA Core, DPDK, OVS
- **nvidia-doca-all** - Full DOCA SDK
- **nvidia-doca-tools** - Management and diagnostic tools

## Verification

```bash
# Check RDMA devices
rdma link

# List RDMA devices
ibv_devices

# Check device capabilities
ibv_devinfo

# Test RDMA performance (requires two nodes)
# On server:
ib_send_bw

# On client:
ib_send_bw <server-ip>
```

## Performance Testing

```bash
# Bandwidth test
ib_send_bw -d mlx5_0 -i 1 -F --report_gbits

# Latency test
ib_send_lat -d mlx5_0 -i 1 -F

# Read bandwidth
ib_read_bw -d mlx5_0 -i 1 -F --report_gbits

# Write bandwidth
ib_write_bw -d mlx5_0 -i 1 -F --report_gbits
```

## Troubleshooting

### Check RoCE Mode

```bash
# Should show "RoCE v2" for modern deployments
rdma link show
```

### Verify Network Configuration

```bash
# Check MTU (should be 9000 for best performance)
ip link show eth0

# Check if interface is up
ip link show eth0 | grep UP
```

## Resources

- [NVIDIA DOCA RoCE Profile](https://docs.nvidia.com/doca/sdk/DOCA-Profiles/)
- [RoCE Configuration Guide](https://docs.nvidia.com/doca/sdk/RDMA-over-Converged-Ethernet/)
- [RDMA Performance Tuning](https://docs.nvidia.com/doca/sdk/)
