
Pass : `iwillteachyoutoberich00CIFER-`
[Password management - User administration · Wazuh documentation](https://documentation.wazuh.com/current/user-manual/user-administration/password-management.html)

- **reload.Sh** :

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

# Ensure the script is run as root
if [ "$(id -u)" -ne 0 ]; then
    handle_error "This script must be run as root"
fi

# Download the Wazuh password tool script
print_message "Downloading the Wazuh password tool script..."
curl -so wazuh-passwords-tool.sh https://packages.wazuh.com/4.7/wazuh-passwords-tool.sh || handle_error "Failed to download the Wazuh password tool script"

# Ask the user for the new password
read -sp "Enter the new password for Wazuh admin: " NEW_PASSWORD
echo

# Change the Wazuh admin password
print_message "Changing the Wazuh admin password..."
sudo bash wazuh-passwords-tool.sh -u admin -p "$NEW_PASSWORD" || handle_error "Failed to change the Wazuh admin password"

print_message "Wazuh admin password changed successfully. Remember to update the password in the Wazuh dashboard and Filebeat nodes if necessary."

# Restart Wazuh API service
print_message "Restarting Wazuh API service..."
sudo systemctl restart wazuh-api || handle_error "Failed to restart Wazuh API service"

# Restart Wazuh manager service
print_message "Restarting Wazuh manager service..."
sudo systemctl restart wazuh-manager || handle_error "Failed to restart Wazuh manager service"

# Restart Filebeat service (if applicable)
print_message "Restarting Filebeat service..."
sudo systemctl restart filebeat || handle_error "Failed to restart Filebeat service"

print_message "Services restarted successfully."

# End of script

```