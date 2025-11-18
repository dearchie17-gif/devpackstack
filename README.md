# devpackstack
Basic definations and uses
DevStack vs PackStack - Quick Notes
# DevStack vs PackStack

| Criteria | **DevStack** | **PackStack** |
|---|---|---|
| **Purpose / Use-case** | Designed for **development and testing**. It’s primarily used by OpenStack developers to spin up a scratch environment. :contentReference[oaicite:0]{index=0} | Intended for **proof-of-concept (POC) / demos**, not production. :contentReference[oaicite:1]{index=1} |
| **Installation Mechanism** | Uses a series of shell scripts (`stack.sh`, `unstack.sh`) to fetch code (often from git) and build components. :contentReference[oaicite:2]{index=2} | Uses **Puppet modules** to configure and install OpenStack. :contentReference[oaicite:3]{index=3} |
| **Supported OS** | Supports Ubuntu, Rocky Linux 9, openEuler, etc. :contentReference[oaicite:4]{index=4} | Supports RHEL / CentOS / RHEL derivatives. :contentReference[oaicite:5]{index=5} |
| **Deployment Topology** | Can do multi-node, but often used in all-in-one for simplicity. :contentReference[oaicite:6]{index=6} | Supports multiple pre-installed hosts over SSH. :contentReference[oaicite:7]{index=7} |
| **Stability and Persistence** | Not meant for production — it's ephemeral, and after a reboot or `unstack.sh`, the setup may not persist. :contentReference[oaicite:8]{index=8} | More stable than DevStack for POC, but still **not production-grade** according to Red-Hat. :contentReference[oaicite:9]{index=9} |
| **Configuration** | Configured via a `local.conf` (or `localrc`) file allowing you to pick what services to enable. :contentReference[oaicite:10]{index=10} | Uses an **answer file**, which users generate and then customize to define which OpenStack services to deploy. :contentReference[oaicite:11]{index=11} |
| **Installation Time** | Relatively fast (~15–30 minutes depending on net speed) for a dev environment. :contentReference[oaicite:12]{index=12} | Can be slower depending on number of nodes, Puppet runs, etc. |
| **Change Management** | Very flexible: you can pull in latest code from Git, re-run, tear-down, modify. Good for testing code changes. | More declarative: changes are made via Puppet and the answer file. Less “bleeding-edge” Git-master style, more stable POC setup. |
| **Ideal Users** | OpenStack developers, contributors, testers, CI systems. | Administrators or users wanting to demo OpenStack on a few nodes, POC environments, early testing. |

---

## Example Code to Generate This Comparison (Python)

Here is a simple Python script that could generate the comparison table above programmatically—useful if you want to include it in documentation or generate HTML from it.

```python
# compare_openstack_installer.py

def get_comparison():
    return {
        "Purpose / Use-case": {
            "DevStack": "Development & testing (for developers)",
            "PackStack": "Proof-of-concept / demo, not production"
        },
        "Installation Mechanism": {
            "DevStack": "Shell scripts (stack.sh, unstack.sh)",
            "PackStack": "Puppet modules over SSH"
        },
        "Supported OS": {
            "DevStack": "Ubuntu, Rocky Linux, openEuler, etc.",
            "PackStack": "RHEL, CentOS and compatibles"
        },
        "Deployment Topology": {
            "DevStack": "Single-node or multi-node (often all-in-one)",
            "PackStack": "Multiple pre-installed hosts via SSH"
        },
        "Stability / Persistence": {
            "DevStack": "Ephemeral; not persistent, not for production",
            "PackStack": "More stable for POC, but not production"
        },
        "Configuration": {
            "DevStack": "local.conf / localrc for enabling services",
            "PackStack": "Answer file (editable) used by Puppet"
        },
        "Installation Time": {
            "DevStack": "Relatively fast (~15–30 mins)",
            "PackStack": "Varies, depends on Puppet runs and number of nodes"
        },
        "Change Management": {
            "DevStack": "Highly flexible, get latest Git code, tear-down & re-run",
            "PackStack": "Declarative via Puppet + answer file"
        },
        "Ideal Users": {
            "DevStack": "OpenStack devs, testers, CI systems",
            "PackStack": "Admins wanting demos, POC on a few nodes"
        }
    }

def print_comparison_md(comparison):
    headers = ["Criteria", "DevStack", "PackStack"]
    print(f"| {' | '.join(headers)} |")
    print(f"|{'---|' * len(headers)}")
    for criteria, vals in comparison.items():
        print(f"| {criteria} | {vals['DevStack']} | {vals['PackStack']} |")

if __name__ == "__main__":
    comp = get_comparison()
    print_comparison_md(comp)

DEVSTACK INSTALLATION:

SETUP STACK USER:
# Create stack user
sudo useradd -s /bin/bash -d /opt/stack -m stack
echo "stack ALL=(ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/stack
sudo su - stack

CLONE AND CONFIGURE:

# Clone repository
git clone https://opendev.org/openstack/devstack
cd devstack

# Create local.conf
cat > local.conf << EOF
[[local|localrc]]
ADMIN_PASSWORD=secret
DATABASE_PASSWORD=\$ADMIN_PASSWORD
RABBIT_PASSWORD=\$ADMIN_PASSWORD
SERVICE_PASSWORD=\$ADMIN_PASSWORD
HOST_IP=<YOUR_IP>
EOF


INSTALL:
# Run installation
./stack.sh

# Source credentials after install
source openrc admin admin

MANAGEMENT COMMANDS:

./stack.sh      # Install/Start
./unstack.sh    # Stop services
./clean.sh      # Remove everything



PACKSTACK INSTALLATION:

PRE-REQUISITES:

# Update system (RHEL/CentOS)
sudo yum update -y

# Install RDO repository
sudo yum install -y centos-release-openstack-yoga

# Disable NetworkManager
sudo systemctl disable NetworkManager
sudo systemctl stop NetworkManager


INSTALL PACKSTACK:
# Install package
sudo yum install -y openstack-packstack

# Generate answer file (optional)
packstack --gen-answer-file=/root/packstack-answers.txt


RUN INSTALLATION:

# All-in-one quick install
sudo packstack --allinone

# OR with answer file
sudo packstack --answer-file=/root/packstack-answers.txt

# Source credentials after install
source /root/keystonerc_admin


PUTTY SSH CONNECTION:

# Basic SSH connection
ssh user@<ip-address>

# Root access
ssh root@<ip-address>

# With private key
ssh -i /path/to/key.pem user@<ip-address>

SYSTEM REQUIREMENTS CHECK:

nproc          # CPU cores (min 4)
free -h        # RAM (min 8GB)
df -h          # Disk space (min 50GB)
cat /etc/os-release  # OS version



