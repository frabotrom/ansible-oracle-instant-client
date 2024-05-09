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
git clone https://github.com/your-github-username/ansible-oracle-instantclient.git
cd ansible-oracle-instantclient
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

```bash
sudo usermod -aG sudo ansible
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
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Configure Linux Hosts
Establishing SSH Connections from the Control Node to Managed Nodes

To manage your Linux hosts using Ansible, it is essential to establish a secure communication channel. SSH (Secure Shell) is the default method used for this purpose. Follow these steps to ensure a smooth setup:

    Generate SSH Keys (if not already done):
        On your control node, generate an SSH key pair if you haven't already. Execute:

        bash

    ssh-keygen -t rsa -b 4096

    Press enter through all prompts to use default settings and no passphrase.

Copy the Public Key to Managed Hosts:

    Use ssh-copy-id to transfer your public key to each managed host. This facilitates password-less authentication from the control node.

    bash

    ssh-copy-id user@managed-host-ip

    Replace user with the remote user's username and managed-host-ip with the IP address of the managed host.

Verify SSH Connection:

    Ensure that you can SSH into the managed host without entering a password:

    bash

    ssh user@managed-host-ip

    Successful login without a password prompt indicates that the SSH setup is correct.

Configure Ansible Inventory:

    Add the managed hosts to your Ansible inventory file with the necessary SSH parameters. Example inventory entry:

    ini

    [servers]
    server1 ansible_host=192.168.1.100 ansible_user=user ansible_ssh_private_key_file=~/.ssh/id_rsa

Test Ansible Communication:

    Run a simple Ansible command to check that Ansible can communicate with the managed hosts:

    bash

        ansible all -m ping

        A successful pong response verifies that the setup is correct.

Configure Windows Hosts
Ensuring Remote Hosts are Configured for HTTPS and WinRM

To manage Windows hosts with Ansible, it's crucial to set up WinRM (Windows Remote Management) and ensure proper HTTPS encryption. Follow these guidelines:

    Enable WinRM:
        On each Windows host, WinRM must be enabled and configured. Execute this in PowerShell as an administrator:

        powershell

    Enable-PSRemoting -Force

Configure WinRM for HTTPS:

    Secure WinRM by enabling HTTPS, which requires a valid certificate. If a certificate is not already installed, create a self-signed certificate:

    powershell

New-SelfSignedCertificate -DnsName "win-host.example.com" -CertStoreLocation Cert:\LocalMachine\My

Configure WinRM to use this certificate:

powershell

    $cert = Get-Certificate -Thumbprint "THUMBPRINT_HERE" -CertStoreLocation Cert:\LocalMachine\My
    winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname="win-host.example.com"; CertificateThumbprint="$cert.Thumbprint"}

Set Group Policies:

    Modify group policies to allow WinRM access, configured via gpedit.msc under:

    rust

    Computer Configuration -> Administrative Templates -> Windows Components -> Windows Remote Management (WinRM) -> WinRM Service

    Set the policy to allow remote server management through WinRM.

Test Connectivity:

    Verify that you can connect from the Ansible control node to the Windows host using the pywinrm Python module, which Ansible utilizes to communicate over WinRM:

    bash

python -m pywinrm -u "user" -p "password" -x "https://win-host.example.com:5986/wsman" "ipconfig"

Successful output indicates that the WinRM setup is correct.

+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++


### x. Configure Linux Hosts

sudo su - ansible

Hacer conexion ssh desde el nodo de control hacia los gestionados

### x. Configure Windows Hosts

Asegurarse de que los hosts remotos tienen un certificado para poder conectarse por https y winrm activo

### 4. Review and Customize Variables

Check the `roles/common/vars/main.yml` and adjust any default values such as paths and version numbers to suit your environment.

### 5. Run the Playbook

Execute the playbooks by specifying your inventory file and the main playbook files:

```bash
ansible-playbook -i inventory/inventario install_linux.yml
```

```bash
ansible-playbook -i inventory/inventario install_windows.yml
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

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details.
