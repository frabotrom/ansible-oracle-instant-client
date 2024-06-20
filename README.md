# Ansible Playbook for Oracle Instant Client and SQL*Plus

This repository contains Ansible playbooks and roles for automating the installation of Oracle Instant Client and SQL*Plus on Windows and Linux hosts. The playbooks handle software deployment, configuration, and ensure the hosts are ready for database connectivity.

## Features

- **Automated Installation**: Easily deploy Oracle Instant Client and SQL*Plus across multiple environments.
- **Cross-platform Support**: Supports both Windows and Linux hosts.
- **Environment Configuration**: Sets up necessary environment variables and PATH adjustments.
- **Flexibility**: Allows installation of different versions of Oracle Instant Client and SQL*Plus based on host requirements.

## Prerequisites

Before you can run these playbooks, ensure you have the following prerequisites met:

- **Ansible Control Node**: A system with Ansible installed and configured to run playbooks. Typically a Linux system, but can also be run from Windows.
- **Target Hosts**: Windows or Linux machines that are accessible via WinRM (for Windows) or SSH (for Linux).
- **Appropriate Credentials**: Administrator (Windows) or sudo-enabled user account (Linux) credentials for accessing the target hosts.
- **Network Access**: Target hosts must be reachable from the control node, with appropriate network configurations to allow Ansible commands.

## Getting Started

To get started with these playbooks, follow the steps outlined below:

### 1. Clone the Repository

Clone this repository to your Ansible control node:

```bash
git clone https://github.com/your-github-username/ansible-oracle-instant-client.git
cd ansible-oracle-instant-client
```

### 2. Install Ansible

```bash
sudo apt-add-repository ppa:ansible/ansible
sudo apt update
sudo apt install ansible
```

```bash
sudo apt-get install adduser
```

```bash
sudo adduser ansible
```

```bash (Debian Systems)
sudo usermod -aG sudo ansible
```
Or alternatively:
```bash (RHEL Systems)
sudo usermod -aG wheel ansible
```

### 3. Configure the Inventory

Modify the `inventory/inventario.ini` file to include the IP addresses, domain names, or aliases for your target hosts under the appropriate groups:

```inventario.ini
[windows]
winhost1.example.com
winhost2.example.com

[windows:vars]
ansible_user=user
ansible_password="password"

[debian]
debianhost1.example.com
debianhost2.example.com

[debian:vars]
ansible_user=user
ansible_password="password"
```

### 4. Configure Managed Hosts
#### 4.1 Configure Linux Hosts

Log in with your previously created ansible user 
```bashh
sudo su - ansible
```

On your control node, generate an SSH key pair if you haven't already.
```bash
ssh-keygen -t rsa -b 4096
```
Press enter through all prompts to use default settings and no passphrase.

Copy the Public Key to Managed Hosts:
```bash
ssh-copy-id user@managed-host-ip
```
Replace user with the remote user's username and managed-host-ip with the IP address of the managed host.

Ensure that you can SSH into the managed host without entering a password
```bash
ssh user@managed-host-ip
```

Add the managed hosts to your Ansible inventory file with the necessary SSH parameters. Example inventory entry:

```inventario.ini
[debian]
debianhost1 ansible_host=192.168.1.100 ansible_user=user ansible_ssh_private_key_file=~/.ssh/id_rsa
```
Test Ansible Communication
```bash
ansible debian -m ping
```

#### 4.2 Configure Windows Hosts

#### REQUIREMENTS: WinRM

It is necessary to have WinRM configured on the Windows host, either using HTTP or HTTPS. **You must have tested the connection from another node in advance**, as this program will not function if the WinRM connection has not been properly established and verified.

Below is an example configuration to set up a WinRM listener using HTTPS:

Execute this in PowerShell as an administrator
```powershell
Enable-PSRemoting -Force
```

Configure WinRM to use this certificate:

```powershell
$dnsName = "prueba.dominio.com"
$cert = New-SelfSignedCertificate -DnsName $dnsName -CertStoreLocation cert:\LocalMachine\My -FriendlyName "WinRM SSL" -NotAfter (Get-Date).AddYears(1)
```

```powershell
Get-ChildItem -Path cert:\LocalMachine\My | Select-Object Subject, Thumbprint
```

Copy the thumbprint of the created certificate and use it to configure WinRM to use HTTPS:

```powershell
winrm create winrm/config/Listener?Address=*+Transport=HTTPS '@{Hostname="prueba.dominio.com"; CertificateThumbprint="<Copied_Thumbprint_Here>"}'
```

Modify group policies to allow WinRM access, configured via gpedit.msc under:
```gpedit.msc
Computer Configuration -> Administrative Templates -> Windows Components -> Windows Remote Management (WinRM) -> WinRM Service -> Allow remote server management through WinRM
```
Edit, select "Enabled" and enter asterisks (*) into the fields then click apply and ok

```powershell
winrm quickconfig -transport:https
```

```powershell
New-NetFirewallRule -DisplayName "WinRM HTTPS" -Direction Inbound -Protocol TCP -LocalPort 5986 -Action Allow
```
Enumerate the WinRM listeners to verify that the HTTPS listener is configured correctly.

```powershell
winrm enumerate winrm/config/Listener
```

Test the connection by running the ping command from the control node
```Control node (linux)
ansible winhost1 -i inventory/inventario.ini -m win_ping
```

### 5. Review and Customize Variables

Check the `roles/linux/vars/main.yml` and `roles/windows/vars/main.yml` to adjust any default values such as paths and version numbers to suit your environment.

### 6. Run the Playbook

Execute the playbooks by specifying your inventory file and the main playbook files:

```bash (for Linux hosts)
ansible-playbook -i inventory/inventario playbooks/install_linux.yml
```

```bash (for Windows hosts
ansible-playbook -i inventory/inventario playbooks/install_windows.yml
```

## Usage

After installation, you can verify the installation of Oracle Instant Client and SQL*Plus by trying to connect to your Oracle Database instance from one of the configured hosts.

## Contributing

Contributions to this repository are welcome. To contribute:

1. Fork the repository.
2. Create a new branch for each feature or improvement.
3. Send a pull request from each feature branch to the main branch.

## Support

If you encounter any issues while using these playbooks, please submit an issue on the GitHub issue tracker.

