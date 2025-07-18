## Purpose

The purpose of this repository is to create a cloud-init script that generates a reproducible and production-ready Rancher deployments across supported on-premises clouds and AWS and Azure.

## How to use

### Prerequisites
ansible

### Usage
#### Example with all variables set
```bash
ansible-playbook -e '
  default_admin=false
  cert_manager_replica_count=2
  cert_manager_version=v1.18.0
  cert_manager_repo=https://charts.jetstack.io
  cert_manager_webhook_replica_count=3
  cert_manager_cainjector_replica_count=2
  rancher_version=v2.11.2
  rancher_hostname=rancher.yourdomain.com
  rancher_bootstrap_password=admin
  rancher_replicas=3
  rancher_repo=https://releases.rancher.com/server-charts/stable
  rancher_vm_ip=127.0.0.1
  rke2_ansible_playbook_url=https://github.com/rancherfederal/rke2-ansible.git
' generate_cloudinit.yml
```

#### Example with minimal variables set
```bash
ansible-playbook generate_cloudinit.ym
```

## Roadmap

| **OS**            | **OS Version** | **Lima** | **Nutanix** | **VMWare** | **Harvester** | **AWS** | **Azure** |
| ----------------- | -------------- | -------- | ----------- | ---------- | ------------- | ------- | -------- |
| **SLES**          | 15             |    x    |     x      |    x      |      x        |    x   |    x    |
| **openSUSE Leap** | 15             |    x    |     x      |    x      |      x        |    x   |    x    |
| **SLE Micro**     | 6              |    x    |     x      |    x      |      x        |    x   |    x    |
| **SUSE Liberty**  | 9              |    x    |     x      |    x      |      x        |    x   |    x    |
|                   | 8              |    x    |     x      |    x      |      x        |    x   |    x    |
| **Oracle Linux**  | 9              |    x    |     x      |    x      |      x        |    x   |    x    |
|                   | 8              |    x    |     x      |    x      |      x        |    x   |    x    |
| **RHEL**          | 9              |    x    |     x      |    x      |      x        |    x   |    x    |
|                   | 8              |    x    |     x      |    x      |      x        |    x   |    x    |
| **Rocky Linux**   | 9              |    ✓    |     x      |    x      |      x        |    x   |    x    |
|                   | 8              |    x    |     x      |    x      |      x        |    x   |    x    |
| **Ubuntu**        | 24             |    x    |     x      |    x      |      x        |    x   |    x    |

## License

This project is licensed under the Apache 2.0 License. See the [LICENSE](LICENSE) file for more information.

## Development Setup and Testing
### For MacOS

#### Prerequisites
- Ansible
- Lima

#### Running the playbook
```bash
echo "Running the following commands:" && \
echo "generate the lima cloud-init config" && \
ansible-playbook generate_cloudinit.yml && \
echo "delete the existing rancher lima instance if it exists" && \
limactl delete -f rancher-lima && \
echo "create the rancher lima instance" && \
limactl create -y rancher-lima.yml --name=rancher-lima && \
echo "start the rancher lima instance and run cloud-init commands" && \
limactl start rancher-lima && \
echo "lima has a bug where it doesn't run cloud-init on successfully on first boot, so we run it manually" && \
limactl shell rancher-lima bash -c 'sudo cloud-init clean -c all && sudo cloud-init init && sudo cloud-init modules --mode config && sudo cloud-init modules --mode final' && \
echo "set up a socks proxy to access the rancher instance" && \
limactl tunnel --socks-port 54447 rancher-lima && \
echo "set up the macOS network settings to use the socks proxy" && \
networksetup -setsocksfirewallproxy wi-fi localhost 54447
```
