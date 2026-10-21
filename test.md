Step 1: Open WSL (Ubuntu)

Open PowerShell on Windows and type:

powershell
wsl
Step 2: Install All Build Tools, Qt6, and System Libraries

Run apt to install the C++ compiler (g++, make), Qt6 modules, and all required library headers (OpenSSL, zlib, libmicrohttpd, libtorrent):

bash
sudo apt update
sudo apt install -y build-essential pkg-config wget \
    qt6-base-dev qt6-base-dev-tools qt6-webengine-dev qt6-webchannel-dev qt6-positioning-dev \
    libssl-dev zlib1g-dev libmicrohttpd-dev libtorrent-rasterbar-dev
Step 3: Download Kiwix's Pre-compiled Dependencies

Kiwix builds libkiwix, libzim, xapian, and icu in CI and hosts the bundle so developers don't have to compile them from source. Download and extract them to your home folder:

bash
cd ~
wget https://tmp.kiwix.org/ci/deps_linux-x86_64-dyn_kiwix-desktop.tar.gz
tar -xzf deps_linux-x86_64-dyn_kiwix-desktop.tar.gz -C $HOME

(This extracts the pre-built libraries into $HOME/BUILD_linux-x86_64/INSTALL).

Step 4: Create the CI Path Symlink

The pre-compiled dependencies were built in GitHub Actions under the /home/runner directory. To make sure all .pc package configs and headers resolve without path errors, link /home/runner to your home directory:

bash
sudo ln -s "$HOME" /home/runner
Step 5: Navigate to the Repository & Switch to PR #1529's Branch

Navigate from WSL into your Windows project directory and checkout the branch for PR #1529:

bash
cd /mnt/c/Users/Swathi/Desktop/kiwix-desktop
git checkout fix/1511-sortable-columns
Step 6: Configure and Compile with qmake6

Set the PKG_CONFIG_PATH so qmake finds the Kiwix dependencies, configure the build, and compile:

bash
# Clean any previous partial build attempts
make clean
# Set pkg-config path to find libkiwix & libzim
export PKG_CONFIG_PATH="$HOME/BUILD_linux-x86_64/INSTALL/lib/x86_64-linux-gnu/pkgconfig"
# Generate the Makefile
qmake6 PREFIX=$HOME/BUILD_linux-x86_64/INSTALL
# Compile using all CPU cores
make -j$(nproc)
Step 7: Run Kiwix Desktop!

Once the compilation finishes, launch the binary:

bash
./kiwix-desktop
