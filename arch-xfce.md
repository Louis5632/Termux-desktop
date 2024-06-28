#!/data/data/com.termux/files/usr/bin/bash

# Define color variables for output
R="$(printf '\033[1;31m')"                           
G="$(printf '\033[1;32m')"
Y="$(printf '\033[1;33m')"
B="$(printf '\033[1;34m')"
C="$(printf '\033[1;36m')"                                       
W="$(printf '\033[0m')"
BOLD="$(printf '\033[1m')"
config_file="$PREFIX/etc/termux-desktop/configuration"

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
for pkg in x11-repo termux-x11-nightly tur-repo pulseaudio proot-distro wget git sudo; do
    if ! is_installed "$pkg"; then
        pkg install -y "$pkg"
    fi
done
clear

# Install Debian using proot-distro
echo "${C}${BOLD}Installing Debian${W}"
proot-distro install debian
sleep 16
clear

# Automatically generate username and password
username="termuxuser"
password=$(openssl rand -base64 12)
echo "${C}${BOLD}Generated Username: ${W}${username}"
echo "${C}${BOLD}Generated Password: ${W}${password}"
echo

# Create a script to run inside the Debian environment
cat <<EOF > /data/data/com.termux/files/home/debian-setup.sh
#!/bin/bash
apt update -y
apt upgrade -y
apt install -y sudo xfce4 xfce4-whiskermenu-plugin mugshot nano passwd

# Create the new user with generated credentials
adduser --disabled-password --gecos "" $username
echo "$username:$password" | chpasswd

# Add the user to the sudo group
usermod -aG sudo $username

# Allow the user to run sudo without a password prompt
echo "$username ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/$username
chmod 440 /etc/sudoers.d/$username
EOF

chmod +x /data/data/com.termux/files/home/debian-setup.sh

# Execute the script inside the Debian environment using proot-distro
proot-distro login debian -- /bin/bash /data/data/com.termux/files/home/debian-setup.sh

# Clean up the setup script
rm /data/data/com.termux/files/home/debian-setup.sh

# Create the start script to launch XFCE desktop
cat <<EOF > $HOME/startxfce4_debian.sh
#!/data/data/com.termux/files/usr/bin/bash

# Kill any existing X11 processes
kill -9 \$(pgrep -f "termux.x11") 2>/dev/null

# Start PulseAudio and X11
pulseaudio --start --load="module-native-protocol-tcp auth-ip-acl=127.0.0.1 auth-anonymous=1" --exit-idle-time=-1
export XDG_RUNTIME_DIR=\${TMPDIR}
termux-x11 :0 >/dev/null &

# Wait for X11 to start
sleep 3

# Launch XFCE desktop as the created user
proot-distro login debian --shared-tmp -- /bin/bash -c "export PULSE_SERVER=127.0.0.1 && export XDG_RUNTIME_DIR=\${TMPDIR} && su - $username -c 'startxfce4'"
EOF

chmod +x $HOME/startxfce4_debian.sh

echo "${G}User $username added successfully and configured.${W}"
echo "${C}To start XFCE4 desktop, type:${W}"
echo "  ./startxfce4_debian.sh"