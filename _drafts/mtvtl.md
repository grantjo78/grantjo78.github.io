# Obtain Linux kernel verion
azurekernel=$(uname -r)

# Search for Azure module  
sudo apt search linux-azure | grep $azurekernel

# Install Azure Linux Modules Extra
sudo apt install linux-modules-extra-$azurekernel -y

# Install additional software
sudo apt install sg3-utils lsscsi build-essential zlib1g-dev mtx -y

# Copy mhvtl repo
git clone https://github.com/markh794/mhvtl.git

# Kernel module build
cd mhvtl/kernel
make
sudo make install

# User Space daemon build
cd ..
make
sudo make install
sudo systemctl daemon-reload
sudo systemctl enable mhvtl.target
sudo systemctl start mhvtl.target
sudo systemctl status mhvtl.target

# Check VTL
lsscsi -g
mtx -f /dev/<insert device id> status
