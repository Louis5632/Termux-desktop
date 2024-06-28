#!/data/data/com.termux/files/usr/bin/bash

# Define color variables for output
R="$(printf '\033[1;31m')"                           
G="$(printf '\033[1;32m')"
Y="$(printf '\033[1;33m')"
B="$(printf '\033[1;34m')"
C="$(printf '\033[1;36m')"                                       
W="$(printf '\033[0m')"
BOLD="$(printf '\033[1m')"

# Clear the terminal and display a header
clear
echo "${Y} ▀█▀ █▀▀ █▀█ █▀▄▀█ █░█ ▀▄▀   █▀▄ █▀▀ █▀ █▄▀ ▀█▀ █▀█ █▀█ ${W}"
echo "${Y} ░█░ ██▄ █▀▄ █░▀░█ █▄█ █░█   █▄▀ ██▄ ▄█ █░█ ░█░ █▄█ █▀▀ ${W}"
echo
echo "${C}${BOLD} Install Gui Desktop In proot${W}"
echo "${C}${BOLD} code by Louis${W}"
sleep 2

# Function to check if a package is installed
is_installed() {
    dpkg -s "$1" &> /dev/null
}

# Install necessary Termux packages if not already installed
echo "${C}${BOLD}Installing proot and dependencies${W}"
for pkg in x11-repo termux-x11 tur-repo pulseaudio proot-distro wget git sudo; do
    if ! is_installed "$pkg"; then
        pkg install -y "$pkg"
    fi
done
clear

# Install Debian using proot-distro
echo "${C}${BOLD}Installing Debian${W}"
proot-distro install debian
sleep 10  # Adjust sleep time as needed
clear

# Automatically generate username and password
username="termuxuser"
password=$(openssl rand -base64 12)
echo "${C}${BOLD}Generated Username: ${W}${username}"
echo "${C}${BOLD}Generated Password: ${W}${password}"
echo

# Create a script to run inside the Debian environment
cat <<'EOF' > $HOME/debian-setup.sh
#!/bin/bash
set -e

# Update and upgrade Debian
apt update
apt upgrade -y

# Install necessary packages for XFCE desktop
apt install -y sudo xfce4 xfce4-goodies mugshot nano

# Create the new user with generated credentials
useradd -m -s /bin/bash $username
echo "$username:$password" | chpasswd

# Add the user to the sudo group
usermod -aG sudo $username

# Allow the user to run sudo without a password prompt
echo "$username ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/$username
chmod 440 /etc/sudoers.d/$username
EOF

chmod +x $HOME/debian-setup.sh

# Execute the script inside the Debian environment using proot-distro
proot-distro login debian -- /bin/bash $HOME/debian-setup.sh

# Clean up the setup script
rm $HOME/debian-setup.sh

# Create the start script to launch XFCE desktop
cat <<'EOF' > $HOME/startxfce4_debian.sh
#!/data/data/com.termux/files/usr/bin/bash

# Kill any existing X11 processes
pkill Xvfb || true

# Start PulseAudio and X11
pulseaudio --start --exit-idle-time=-1
export DISPLAY=:0

# Launch XFCE desktop
proot-distro login debian --shared-tmp -- /bin/bash -c "su - $username -c 'startxfce4'"
EOF

chmod +x $HOME/startxfce4_debian.sh

echo "${G}User $username added successfully and configured.${W}"
echo "${C}To start XFCE4 desktop, type:${W}"
echo "  ./startxfce4_debian.sh"
