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
