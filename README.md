# OpenShift 4.16 Baremetal IPI Deployment - ocp-bm-dev01

This repository contains a structured workflow and supporting files for deploying an OpenShift 4.16 cluster using the Baremetal IPI method.

## Directory Structure

- `deploy/`: Contains scripts organized by deployment phase.
- `manifests/`: Contains OpenShift manifests and custom configurations.

# OpenShift 4.16 IPI Bare Metal Installation Prerequisites

This document outlines the complete prerequisites for installing OpenShift 4.16 using Installer-Provisioned Infrastructure (IPI) on bare metal.

---

## 1. Node Requirements

- **Baremetal Provisioner Node**: RHEL 9.x installed; used only during installation.
- **Control Plane Nodes**: Minimum of 3 for high availability.
- **Worker Nodes**: Optional, but at least 2 recommended for production.
- **CPU Architecture**: x86_64 or aarch64.
- **Uniformity**: Nodes should be identical per role (same brand/model/specs).
- **BMC Access**: Required for all nodes via IPMI, Redfish, or proprietary protocols.
- **Latest Generation Hardware**: Must support RHEL 9.x and RHCOS 9.x.
- **Network Interfaces**:
  - At least one NIC for the routable baremetal network.
  - One NIC for provisioning network (PXE boot), if used.
- **UEFI Boot**: Required when using IPv6 on provisioning network.
- **Secure Boot**:
  - **Manual**: Requires Redfish virtual media.
  - **Managed**: Supported only on specific HPE/Dell hardware.

---

## 2. Minimum Resource Requirements

| Role         | OS    | CPU | RAM   | Storage | IOPS |
|--------------|-------|-----|-------|---------|------|
| Bootstrap    | RHEL  | 4   | 16 GB | 100 GB  | 300  |
| Control Plane| RHCOS | 4   | 16 GB | 100 GB  | 300  |
| Compute      | RHCOS | 2   | 8 GB  | 100 GB  | 300  |

---

## 3. OpenShift Virtualization Planning

- **Live Migration**: Requires ≥2 worker nodes.
- **Storage**: Must support RWX access mode.
- **SR-IOV**: NICs must be supported.

---

## 4. Firmware Requirements (Redfish Virtual Media)

- **HP**: iLO6 ≥ 1.57, iLO5 ≥ 2.63
- **Dell**: iDRAC 9 ≥ v6.10.30.00
- **Cisco UCS**: CIMC ≥ 5.2(2)

---

## 5. Network Requirements

- **Networks**:
  - **Provisioning Network**: Optional, non-routable.
  - **Baremetal Network**: Required, routable.
- **Ports**: Must be open for DHCP, TFTP, NTP, Ironic, etc.  
  (e.g., 67–68, 69, 80, 123, 5050–5051, 6180–6183, 6385–6388, 8080–8083, 9999)
- **MTU**: ≥1500 recommended.
- **NIC Configuration**: PXE boot for provisioning NIC; VLAN separation if applicable.

---

## 6. DNS Requirements

- **Records Needed**:
  - `api.<cluster_name>.<base_domain>` (A/AAAA + PTR)
  - `*.apps.<cluster_name>.<base_domain>` (wildcard A/AAAA)
- **DNS Server**: Must support TCP and UDP.

---

## 7. DHCP Requirements

- **Provisioning Network**: Only one DHCP server allowed (dnsmasq by default).
- **Baremetal Network**: IP reservations required for:
  - API VIP
  - Ingress VIP
  - Provisioner node
  - Control plane nodes
  - Worker nodes
