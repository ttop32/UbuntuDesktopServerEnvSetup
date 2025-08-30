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
sudo apt-get install vim curl dkms build-essential htop ncdu net-tools

#ssh
sudo apt-get install ssh
sudo sed -i 's/^#?Port .*/Port 2222/' /etc/ssh/sshd_config
sudo service ssh start


#git
sudo apt install git
ssh-keygen -t rsa -b 4096 -C "ttop324@gmail.com"
cat ~/.ssh/id_rsa.pub
# copy and paste cat result to https://github.com/settings/ssh/new
ssh -T git@github.com
#git lfs - Git Large File Storage 
curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | sudo bash
sudo apt-get install git-lfs
git lfs install
sudo apt install npm















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
git clone https://github.com/morrownr/88x2bu-20210702.git
cd 88x2bu-20210702
sudo ./install-driver.sh



#wifi connect
iwconfig
sudo apt-get install network-manager
sudo service NetworkManager restart
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
sudo sed -i -e "\$aUUID=DC94-944F /mnt/0/ exfat defaults,uid=1000,gid=1000 0 0" /etc/fstab
sudo mount -a
sudo systemctl daemon-reload
df -h















########################software install


#google drive clone
sudo apt update && sudo apt install rclone
rclone config 

##chrome install
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo apt install ./google-chrome-stable_current_amd64.deb

#ngrok
sudo snap install ngrok
ngrok authtoken brtg34yh645u6577j6ye
ngrok http 8080

#tmux
sudo apt-get install tmux
tmux new -s 0
tmux attach -t 0
vim ~/.tmux.conf
""" input below line

set -g history-limit 50000
set -g mouse on
setw -g mode-keys vi
bind-key y set-window-option synchronize-panes
bind -n M-Left  select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up    select-pane -U
bind -n M-Down  select-pane -D
bind -n M-q     display-panes
bind -n M-0 select-window -t 0
bind -n M-1 select-window -t 1
bind -n M-2 select-window -t 2
bind -n M-3 select-window -t 3
bind -n M-4 select-window -t 4
bind -n M-5 select-window -t 5
bind -n M-6 select-window -t 6
bind -n M-7 select-window -t 7
bind -n M-8 select-window -t 8
bind -n M-9 select-window -t 9
bind -n S-Left  previous-window
bind -n S-Right next-window
bind | split-window -h
bind - split-window -v
"""
tmux source ~/.tmux.conf


#anaconda for python3
wget https://repo.anaconda.com/archive/Anaconda3-2024.06-1-Linux-x86_64.sh	
bash Anaconda3-2024.06-1-Linux-x86_64.sh	
conda config --set ssl_verify False

#pip 
pip install Pillow numpy numpy

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
conda install nb_conda_kernels
conda install -c conda-forge jupyterlab_code_formatter
conda install black isort
jupyter lab

#vscode server
curl -fsSL https://code-server.dev/install.sh | sh
sudo vim ~/.config/code-server/config.yaml
"""
bind-addr: 0.0.0.0:8080
auth: password
password: 12343543
cert: false
"""
code-server



#RTX30-GPUs for tensorflow 1.15.4
conda create --name tf1-nv  python=3.6
conda activate tf1-nv
conda install pip
pip install --user nvidia-pyindex
conda install -c conda-forge openmpi
export PATH=$PATH:$HOME/.local/bin
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$HOME/anaconda3/envs/tf1-nv/lib/
pip install --user nvidia-tensorflow[horovod]

#tf2.6.0
conda create --name tf26  python=3.6
conda activate tf26
conda install cudatoolkit=11.2 -c pytorch -c nvidia
conda install -c conda-forge cudnn-8.2.1.32  
!pip install tensorflow_gpu==2.6.0


# pytorch
pip install torch==1.7.1+cu110 torchvision==0.8.2+cu110 torchaudio===0.7.2 -f https://download.pytorch.org/whl/torch_stable.html
# pytorch conda
conda install pytorch torchvision torchaudio cudatoolkit=11.1 -c pytorch -c nvidia


#show conda kernel to jupyter lab
conda create -n tf2 python=3.7
conda activate tf2
conda install ipykernel
pip install ipython_genutils
ipython kernel install --user --name=tf2
conda deactivate

#py39
conda create -n py39 python=3.9
conda activate py39
conda install ipykernel
pip install ipython_genutils
ipython kernel install --user --name=py39
conda deactivate
history














#ddns ==========================================================================
#################### ddns generate from www.duckdns.org/
#get token from website to update dynamic ip every 5 min 
vim ~/duck.sh
"""
echo url="https://www.duckdns.org/update?domains=EXAMPLDOMAINNAME&token=1534634-535f-4536-a435-4435242543&ip=" | curl -k -o ~/duck.log -K -
"""
chmod 700 ~/duck.sh
crontab -e
"""
*/5 * * * * ~/duck.sh >/dev/null 2>&1
"""    
./duck.sh
############################ddns generate from www.dynu.com
URL='https://www.dynu.com/support/downloadfile/31'; FILE=`mktemp`; wget "$URL" -qO $FILE && sudo dpkg -i $FILE; rm $FILE
sudo vim /etc/dynuiuc/dynuiuc.conf
systemctl restart dynuiuc.service


#reverse proxy and ssl==========================================================================
#port forward 80 and 443 is requried
#set nginx reverse proxy and issue https ssl to generated url using Let's Encrypt
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get purge nginx nginx-common
sudo apt-get install python3-certbot-nginx
sudo certbot --nginx -d ttop324.ddns.net


#setup nginx confing and reload
sudo vim /etc/nginx/nginx.conf
sudo vim /etc/nginx/sites-enabled/default
sudo nginx -t
sudo systemctl restart nginx && sudo systemctl status nginx
sudo nginx -s reload
"""
#ssl reverse proxy
server {
    listen 80;
    server_name example.com;
    client_max_body_size 0;
    
    #jupyter lab
    location / {
            proxy_pass http://127.0.0.1:22222/;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header Host $host;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

            # websocket headers
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_set_header X-Scheme $scheme;

            proxy_buffering off;
    }
    #VS code server
    location /code-server/ {
            proxy_pass http://127.0.0.1:33333/;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            proxy_set_header X-NginX-Proxy true;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_redirect off;
    }
    #testing page
    location /test/ {
            proxy_pass http://127.0.0.1:3000/;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection upgrade;
            proxy_set_header Host $host;
            proxy_set_header Accept-Encoding gzip;
    }

    
    listen [::]:443 ssl ipv6only=on; # managed by Certbot
    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}

#redirect http://example.com to https://example.com
server {
    if ($host = example.com) { 
        return 301 https://$host$request_uri;
    } # managed by Certbot
    
    listen 80 ;
    listen [::]:80 ;
    server_name example.com;
    return 404; # managed by Certbot
}

#sub domain
server {
    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;
    server_name  sub.example.com;
    location / {
            proxy_pass http://127.0.0.1:8080/;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection upgrade;
            proxy_set_header Host $host;
            proxy_set_header Accept-Encoding gzip;
    }
}
"""


#auto renew for ssl
sudo vim /etc/cron.d/certbot
"""
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
0 */12 * * * root test -x /usr/bin/certbot -a \! -d /run/systemd/system && perl -e 'sleep int(rand(43200))' && certbot -q renew
"""









            
#  docker install
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
sudo apt update
sudo apt install docker-ce
docker --version
sudo chmod 666 /var/run/docker.sock
docker login


#docker test
vim Dockerfile
"""
FROM node:10
WORKDIR /workspace
COPY package*.json ./
RUN npm install
COPY . .

EXPOSE 80

CMD [ "node", "index.js" ]
"""
docker build -t ttop324/nodejs_hello_world:latest .
docker images
docker run -p 10000:80 -d ttop324/nodejs_hello_world:latest
curl -i localhost:10000
docker ps -all
#upload download
docker push ttop324/nodejs_hello_world:latest 
docker pull ttop324/nodejs_hello_world:latest
#remove
docker rm -f 1gvt5t45
docker rmi vcrt542
    


#increase virtual memory size
free -m
sudo swapoff -v /swapfile
sudo fallocate -l 32G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

vim /etc/fstab 
'''
swapfile none swap sw 0 0
'''





#security########################################################
#check ssh login
tail -f -n 500 /var/log/auth.log | grep 'sshd'

#check syslog
cat /var/log/syslog

#check job scheduler
sudo crontab -u userName -l

#change root pw
sudo -i
passwd

#remove root ssh login
vim /etc/ssh/sshd_config
add "PermitRootLogin no"
systemctl restart sshd



#login try 
journalctl -xe
last
last -f /var/log/btmp | more
#ip ban
iptables -A INPUT -s 47.243.126.246 -j DROP


#fail ban
sudo apt-get install fail2ban 
sudo vi /etc/fail2ban/jail.conf
"""
[DEFAULT]
ignoreip = 127.0.0.1/8 192.168.0.1/24   # 로컬환경 항상 허용
bantime = 2592000      # 30일 차단 (초단위)
maxretry = 3        # 최대 재시도 횟수. 이 이상되면 차단됨.

"""
sudo vi /etc/fail2ban/jail.d/sshd-ddos.conf
"""
[sshd-ddos] 
enabled = true
"""
sudo service fail2ban restart
#list ban
cat /var/log/fail2ban.log  | grep fail2ban.actions
#unban
sudo fail2ban-client set sshd unbanip 000.000.000.000

#geoip-ban#######################
sudo apt-get install geoip-bin geoip-database
geoiplookup 8.8.8.8

vim /usr/local/bin/sshfilter.sh
"""
#!/bin/bash
 
# UPPERCASE space-separated country codes to ACCEPT
ALLOW_COUNTRIES="KR"
 
if [ $# -ne 1 ]; then
  echo "Usage:  `basename $0` <ip>" 1>&2
  exit 0 # return true in case of config issue
fi
 
COUNTRY=`/usr/bin/geoiplookup $1 | awk -F ": " '{ print $2 }' | awk -F "," '{ print $1 }' | head -n 1`
 
[[ $COUNTRY = "IP Address not found" || $ALLOW_COUNTRIES =~ $COUNTRY ]] && RESPONSE="ALLOW" || RESPONSE="DENY"
 
if [ $RESPONSE = "ALLOW" ]
then
  exit 0
else
  logger "$RESPONSE sshd connection from $1 ($COUNTRY)"
  exit 1
fi
"""
sudo vim /etc/hosts.deny 
"""
sshd: ALL
"""
sudo vim /etc/hosts.allow 
"""
sshd: ALL: aclexec /usr/local/bin/sshfilter.sh %a
"""

sudo chown root.root /usr/local/bin/sshfilter.sh
sudo chmod 775 /usr/local/bin/sshfilter.sh
/usr/local/bin/sshfilter.sh 8.8.8.8
/usr/local/bin/sshfilter.sh 14.32.209.211
service sshd restart 



  ```


# Acknowledgement and References
- [TensorFlow-1-15-for-NVIDIA-RTX30-GPUs](https://www.pugetsystems.com/labs/hpc/How-To-Install-TensorFlow-1-15-for-NVIDIA-RTX30-GPUs-without-docker-or-CUDA-install-2005/)  
- [A3000u wifi usb driver](https://awakening95.tistory.com/10)
- [tmux setup](https://junho85.pe.kr/320)
- [fail2ban](https://zipi.me/601)
- [geoip-ban](https://shutcoding.tistory.com/24)
