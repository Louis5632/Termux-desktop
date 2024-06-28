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

echo "${C}${BOLD}Installing Debian${W}"
proot-distro install debian
sleep 16
clear

# Prompt for username and password
read -p "Enter the username for the new user: " username
read -sp "Enter the password for the new user: " password
echo

# Create a script to run inside the Debian environment
cat <<EOF > /data/data/com.termux/files/home/debian-setup.sh
#!/bin/bash
apt update -y
apt upgrade -y
apt install -y sudo xfce4 xfce4-whiskermenu-plugin mugshot nano passwd

# Create the new user
adduser --disabled-password --gecos "" $username
echo "$username:$password" | chpasswd

# Add the user to the sudo group
usermod -aG sudo $username

# Allow the user to run sudo without a password prompt
echo "$username ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/$username
chmod 440 /etc/sudoers.d/$username
EOF

chmod +x /data/data/com.termux/files/home/debian-setup.sh

# Execute the script inside the Debian environment
proot-distro login debian -- /bin/bash /data/data/com.termux/files/home/debian-setup.sh

# Clean up the setup script
rm /data/data/com.termux/files/home/debian-setup.sh

# Create the start script with the specified username
cat <<EOF > $HOME/startxfce4_debian.sh
#!/data/data/com.termux/files/usr/bin/bash

# Kill open X11 processes
kill -9 \$(pgrep -f "termux.x11") 2>/dev/null

# Enable PulseAudio over Network
pulseaudio --start --load="module-native-protocol-tcp auth-ip-acl=127.0.0.1 auth-anonymous=1" --exit-idle-time=-1

# Prepare termux-x11 session
export XDG_RUNTIME_DIR=\${TMPDIR}
termux-x11 :0 >/dev/null &

# Wait a bit until termux-x11 gets started.
sleep 3

# Launch Termux X11 main activity
am start --user 0 -n com.termux.x11/com.termux.x11.MainActivity > /dev/null 2>&1
sleep 1

# Login in PRoot Environment. Do some initialization for PulseAudio, /tmp directory
# and run XFCE4 as user.
proot-distro login debian --shared-tmp -- /bin/bash -c  'export PULSE_SERVER=127.0.0.1 && export XDG_RUNTIME_DIR=\${TMPDIR} && su - $username -c "env DISPLAY=:0 startxfce4"'

exit 0
EOF

# Make the start script executable
chmod +x $HOME/startxfce4_debian.sh

echo "${G}User $username added successfully and configured.${W}"
echo "${C}To start XFCE4 desktop, type:${W}"
echo "  ./startxfce4_debian.sh"
