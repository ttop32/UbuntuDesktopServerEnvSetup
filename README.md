# UbuntuDesktopServerEnvSetup
personal ubuntu desktop server setup note

setup note to run python jupyter lab from outdoor

# Hardware Environment
- gpu             3060GTX
- usb lan card    iptime A3000u
- modem           ipTIME A3004NS-M
- os              ubuntu 18.04
- hard            wd new my book




# Environment setup note

```python

#change apt mirror to korea server for apt down speed
sudo sed -i 's/kr.archive.ubuntu.com/mirror.kakao.com/g' /etc/apt/sources.list


#apt install
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install vim curl dkms build-essential htop ncdu


#ssh
sudo apt-get install ssh
sed -i 's/^#?Port .*/Port 2222/' /etc/ssh/sshd_config
service ssh start


#gpu driver
#sudo add-apt-repository ppa:graphics-drivers/ppa
#sudo apt update
#sudo ubuntu-drivers autoinstall
#sudo reboot
#nvidia-smi



##################################gpu   https://www.tensorflow.org/install/gpu?hl=ko
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600`
sudo add-apt-repository "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"
sudo apt-get update

wget http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb

sudo apt install ./nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb
sudo apt-get update

# Install NVIDIA driver
sudo apt-get install --no-install-recommends nvidia-driver-455
# Reboot. Check that GPUs are visible using the command: nvidia-smi

wget https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/libnvinfer7_7.1.3-1+cuda11.0_amd64.deb
sudo apt install ./libnvinfer7_7.1.3-1+cuda11.0_amd64.deb
sudo apt-get update

# Install development and runtime libraries (~4GB)
sudo apt-get install --no-install-recommends \
    cuda-11-0 \
    libcudnn8=8.0.4.30-1+cuda11.0  \
    libcudnn8-dev=8.0.4.30-1+cuda11.0

# Install TensorRT. Requires that libcudnn8 is installed above.
sudo apt-get install -y --no-install-recommends libnvinfer7=7.1.3-1+cuda11.0 \
    libnvinfer-dev=7.1.3-1+cuda11.0 \
    libnvinfer-plugin7=7.1.3-1+cuda11.0
















#A3000u wifi usb driver
sudo apt-get update
sudo apt-get install build-essential dkms git
sudo git clone https://github.com/cilynx/rtl88x2BU_WiFi_linux_v5.3.1_27678.20180430_COEX20180427-5959.git
cd rtl88x2BU_WiFi_linux_v5.3.1_27678.20180430_COEX20180427-5959
VER=$(sed -n 's/\PACKAGE_VERSION="\(.*\)"/\1/p' dkms.conf)
sudo rsync -rvhP ./ /usr/src/rtl88x2bu-${VER}
sudo dkms add -m rtl88x2bu -v ${VER}
sudo dkms build -m rtl88x2bu -v ${VER}
sudo dkms install -m rtl88x2bu -v ${VER}
sudo modprobe 88x2bu

#wifi connect
iwconfig
sudo apt-get install network-manager
sudo service network-manager restart
sudo nmtui
"""
activate a connection - select wifi
"""

#iptime router setting
"""
open http://192.168.0.1/
fix local ip : newtork - dhcp            //fix desktop local ip
portfowrd : router - portforwarding      //port forward for jupyterlab(port 8888) and ssh(port 22) 
ddns : utility - ddns                 //get xxxx.iptime.org
"""

#hdd mount
df -h
sudo fdisk -l
sudo blkid
sudo mkdir -p /mnt/0/
sed -i -e "\$aUUID=DC94-944F /mnt/0/ exfat defaults,uid=1000,gid=1000 0 0" /etc/fstab
sudo mount -a
df -h


#error fix for Possible missing firmware /lib/firmware/rtl_nic/rtl8125a-3.fw 
sudo su
cd /lib/firmware/rtl_nic
wget https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/plain/rtl_nic/rtl8125a-3.fw
wget https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/plain/rtl_nic/rtl8168fp-3.fw
update-initramfs -u





#git
sudo apt install git
git config --global user.name "ttop32"
git config --global user.email ttop324@gmail.com



#tmux
sudo apt-get install tmux

tmux new -s 0
tmux attach -t 0

vim ~/.tmux.conf
""" input below line
set -g mouse on
setw -g mode-keys vi

// Use Alt-arrow keys without prefix key to switch panes
bind -n M-Left select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up select-pane -U
bind -n M-Down select-pane -D

//Shift arrow to switch windows
bind -n S-Left  previous-window
bind -n S-Right next-window

//scrollback buffer size increase
set -g history-limit 100000

//change window order
bind-key -n C-S-Left swap-window -t -1
bind-key -n C-S-Right swap-window -t +1
"""
tmux source ~/.tmux.conf





#anaconda for python3
wget https://repo.anaconda.com/archive/Anaconda3-2020.02-Linux-x86_64.sh
bash Anaconda3-2020.02-Linux-x86_64.sh
conda config --set ssl_verify False

#RTX30-GPUs for tensorflow 1.15.4
conda create --name tf1-nv  python=3.6
conda activate tf1-nv
conda install pip
pip install --user nvidia-pyindex
conda install -c conda-forge openmpi
export PATH=$PATH:$HOME/.local/bin
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$HOME/anaconda3/envs/tf1-nv/lib/
pip install --user nvidia-tensorflow[horovod]


# pytorch
pip install torch==1.7.1+cu110 torchvision==0.8.2+cu110 torchaudio===0.7.2 -f https://download.pytorch.org/whl/torch_stable.html



#jupyter
conda install jupyter
jupyter notebook password
jupyter notebook --generate-config
vim ~/.jupyter/jupyter_notebook_config.py
""" input below line
c.NotebookApp.ip = '0.0.0.0'
c.NotebookApp.open_browser = False
c.NotebookApp.port = 8888
"""
conda install -c conda-forge jupyterlab
jupyter lab





#pip 
pip install Pillow
pip install numpy 
pip install tqdm



#google drive
sudo apt update && sudo apt install rclone
rclone config 

  ```


# Acknowledgement and References
- [TensorFlow-1-15-for-NVIDIA-RTX30-GPUs](https://www.pugetsystems.com/labs/hpc/How-To-Install-TensorFlow-1-15-for-NVIDIA-RTX30-GPUs-without-docker-or-CUDA-install-2005/)  
- [A3000u wifi usb driver](https://awakening95.tistory.com/10)
- [tmux setup](https://junho85.pe.kr/320)
