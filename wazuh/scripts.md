**setup SSH Server**

```bash
#!/bin/bash

# Function to print messages
print_message() {
    echo -e "\n====================\n$1\n====================\n"
}

# Function to handle errors
handle_error() {
    echo "Error: $1"
    exit 1
}

# Update the system
print_message "Cleaning YUM cache and updating the system..."
sudo yum clean all || handle_error "Failed to clean YUM cache"
sudo yum update -y || handle_error "Failed to update the system"

# Install necessary packages including OpenSSH
print_message "Installing necessary packages: vim, git, curl, wget, unzip, openssh-server..."
sudo yum install -y vim git curl wget unzip openssh-server || handle_error "Failed to install necessary packages"

# Enable and start the SSH service
print_message "Enabling and starting the SSH service..."
sudo systemctl enable sshd || handle_error "Failed to enable SSH service"
sudo systemctl start sshd || handle_error "Failed to start SSH service"

# Optional: Add a new user and set password
# Uncomment the lines below if you want to create a new user
# NEW_USER="myuser"
# NEW_USER_PASSWORD="cifer"
# print_message "Creating a new user '$NEW_USER' and setting the password..."
# sudo adduser $NEW_USER || handle_error "Failed to add new user '$NEW_USER'"
# echo "$NEW_USER:$NEW_USER_PASSWORD" | sudo chpasswd || handle_error "Failed to set password for new user '$NEW_USER'"

# Open the SSH port in the firewall (if not already open)
print_message "Opening the SSH port in the firewall..."
sudo firewall-cmd --permanent --add-service=ssh || handle_error "Failed to open SSH port in the firewall"
sudo firewall-cmd --reload || handle_error "Failed to reload the firewall"

# Verify SSH service status
print_message "Verifying SSH service status..."
sudo systemctl status sshd || handle_error "Failed to verify SSH service status"

# Print success message
print_message "Setup complete. SSH is enabled."

# End of script

```

**updating wazuh agent**

```bash
#!/bin/bash

# New Wazuh server address
NEW_WAZUH_SERVER="NEW_WAZUH_SERVER_IP_OR_HOSTNAME"

# Function to print messages
print_message() {
    echo -e "\n====================\n$1\n====================\n"
}

# Function to handle errors
handle_error() {
    echo "Error: $1"
    exit 1
}

# Check if running as root
if [[ $EUID -ne 0 ]]; then
   handle_error "This script must be run as root"
fi

# Backup the current configuration file
print_message "Backing up the current Wazuh configuration file..."
cp /var/ossec/etc/ossec.conf /var/ossec/etc/ossec.conf.bak || handle_error "Failed to backup the configuration file"

# Update the Wazuh server address in the configuration file
print_message "Updating the Wazuh server address..."
sed -i "s|<address>.*</address>|<address>$NEW_WAZUH_SERVER</address>|g" /var/ossec/etc/ossec.conf || handle_error "Failed to update the Wazuh server address"

# Restart the Wazuh agent
print_message "Restarting the Wazuh agent..."
systemctl restart wazuh-agent || handle_error "Failed to restart the Wazuh agent"

# Verify the Wazuh agent status
print_message "Verifying the Wazuh agent status..."
systemctl status wazuh-agent || handle_error "Failed to verify the Wazuh agent status"

# Print success message
print_message "Wazuh server address updated successfully to '$NEW_WAZUH_SERVER'"

# End of script


```