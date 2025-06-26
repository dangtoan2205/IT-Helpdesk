# OpenVPN Installation and Management Script

This script automates the installation and management of an OpenVPN server on Debian, Ubuntu, CentOS, Amazon Linux 2, Fedora, Oracle Linux 8, Arch Linux, Rocky Linux, and AlmaLinux. It includes custom features to set expiration dates for VPN users and automatically revoke them when expired. Configuration files (`.ovpn`) are saved to `/home/ubuntu`.

Original repository: `angristan/openvpn-install`

## Features

- Installs and configures OpenVPN server.
- Adds new VPN users with customizable expiration dates.
- Automatically revokes users when their expiration date is reached.
- Saves client configuration files to /home/ubuntu.
- Supports password-protected client keys.
- Manages DNS resolvers, compression, and encryption settings.
- Includes a cron job to check for expired clients daily.
- Logs expiration checks to `/var/log/openvpn-expiry.log`.

## Prerequisites

- A server running a supported OS (e.g., Ubuntu 20.04 or later).
- Root access to the server.
- The `ubuntu` user account exists (or modify the script to use a different user for .ovpn file ownership).
- Internet connectivity for package installation.

## Installation

1. **Download the Script**:

```bash
wget https://raw.githubusercontent.com/your-repo/openvpn-install/master/openvpn-install.sh
```

Replace `your-repo` with the actual repository if hosted online, or copy the script to your server.

2. **Make the Script Executable**:

```bash
chmod +x openvpn-install.sh
```

3. **Run the Script**:

```bash
sudo ./openvpn-install.sh
```

  - If OpenVPN is not installed, the script will prompt for configuration options (e.g., IP address, port, protocol, DNS).
  - Follow the prompts to set up the server. Default values are provided for convenience. The script will install OpenVPN, configure it, and create one user.

## Usage


### Adding a New VPN User

1. **Run the Script:**

```bash
sudo ./openvpn-install.sh
```

  Select option `1) Add a new user`.

2. **Provide Client Details:**


- **Client Name**: Enter a unique alphanumeric name for the client (e.g., `alice`).
- **Expiration Days**: Specify the number of days the VPN user should be active (e.g., `30`).
- **Password Protection**: Choose whether to add a passwordless client (`1`) or a password-protected key (`2`).
  - If selecting `2`, you’ll be prompted to set a password for the client key.

3. **Retrieve the Configuration File**:

- The `.ovpn` file is saved to `/home/ubuntu/<client-name>.ovpn`.
- Example: For client `alice`, the file is `/home/ubuntu/alice.ovpn`.
- Transfer this file to your OpenVPN client device (e.g., via SCP or SFTP`):

```bash
scp ubuntu@your-server-ip:/home/ubuntu/alice.ovpn .
```

 - Import the `.ovpn` file into your OpenVPN client software (e.g., OpenVPN Connect).

## Revoking a VPN User

1. **Run the Script**:

```bash
sudo ./openvpn-install.sh
```

Select option `2) Revoke existing user`.

2. **Select Client to Revoke**

- A list of active clients will be displayed.
- Eter the number corresponding to the client you want to revoke (e.g., `1` for the first client).

3. **Confirmation**:

- The client’s certificate will be revoked, the `.ovpn` file will be deleted from `/home/ubuntu`, and the expiration record will be removed.
- The client will no longer be able to connect.

### Automatic Expiration

- **Mechanism**: The script sets up a cron job (`/etc/cron.d/openvpn-expiry`) that runs daily at midnight to check for expired clients.
- **Expiration Check**:
  - Expiration dates are stored in `/etc/openvpn/clients/<client-name>.expiry`.
  - If the current time exceeds the expiration date, the client is automatically revoked.

- **Logs**: Check `/var/log/openvpn-expiry.log` for details of revocation actions.

```bash
cat /var/log/openvpn-expiry.log
```

- **Manual Check**: To manually trigger the expiration check:

```bash
sudo /usr/local/bin/check-openvpn-expiry.sh
```

## Removing OpenVPN

1. **Run the Script**:

```bash
sudo ./openvpn-install.sh
```

  - Select option `3) Remove OpenVPN`.

2. **Confirm Removal**:

- Enter `y` to confirm.
- The script will remove OpenVPN, configuration files, cron jobs, logs, and iptables rules.

3. **Post-Removal**:

- If Unbound was installed as a DNS resolver, you’ll be prompted to remove it completely or keep it.

## File Locations

- **Server Configuration**: `/etc/openvpn/server.conf`
- **Client Configurations**: `/home/ubuntu/<client-name>.ovpn`
- **Expiration Records**: `/etc/openvpn/clients/<client-name>.expiry`
- **Expiration Check Script**: `/usr/local/bin/check-openvpn-expiry.sh`
- **Cron Job**: `/etc/cron.d/openvpn-expiry`
- **Expiration Logs**: `/var/log/openvpn-expiry.log`

## Troubleshooting

- **Client Cannot Connect**:

  - Ensure the `.ovpn` file is correctly imported.
  - Verify the server’s public IP and port are accessible.
  - Check if the client has been revoked (`/etc/openvpn/easy-rsa/pki/index.txt`).
    
- **Expiration Not Working**:
  - Verify the cron job exists: `cat /etc/cron.d/openvpn-expiry`.
  - Check permissions of `/usr/local/bin/check-openvpn-expiry.sh` (`755`).
  - Inspect `/var/log/openvpn-expiry.log` for errors.

- **No** `.ovpn` **File**:
  - Ensure `/home/ubuntu` exists and is writable by the `ubuntu` user.
  - Check script output for errors during client creation.

- **Cron Job Not Running**:
  - Ensure `cron` is installed and running: `sudo systemctl status cron`.
  - Test the cron script manually: `sudo /usr/local/bin/check-openvpn-expiry.sh`.

## Notes

- **Backup**: Always back up configuration files before modifying or removing OpenVPN.
- **Custom User**: If using a different user instead of ubuntu, modify the script to change ownership of `.ovpn` files (search for `chown ubuntu:ubuntu`).
- **Cron Schedule**: To change the expiration check frequency, edit `/etc/cron.d/openvpn-expiry` (e.g., `*/5 * * * *` for every 5 minutes).
- **Security**: Restrict access to `/etc/openvpn/client` and `/home/ubuntu` to prevent unauthorized access to sensitive files.

## Contributing

This script is a modified version of angristan/openvpn-install. Contributions are welcome! Please submit issues or pull requests to the original repository or your fork.

## License

This script is licensed under the MIT License. See the original repository for details.
