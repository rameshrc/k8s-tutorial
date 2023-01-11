sudo apt update
sudo apt install vim wget curl

export RELEASE=$(curl -s https://api.github.com/repos/etcd-io/etcd/releases/latest|grep tag_name | cut -d '"' -f 4)
wget https://github.com/etcd-io/etcd/releases/download/${RELEASE}/etcd-${RELEASE}-linux-amd64.tar.gz

tar xvf etcd-${RELEASE}-linux-amd64.tar.gz

cd etcd-${RELEASE}-linux-amd64

sudo mv etcd etcdctl etcdutl /usr/local/bin 

$ etcd --version
$ etcdctl version
$ etcdutl version

sudo mkdir -p /var/lib/etcd/
sudo mkdir /etc/etcd

sudo groupadd --system etcd
sudo useradd -s /sbin/nologin --system -g etcd etcd

sudo chown -R etcd:etcd /var/lib/etcd/

sudo vim /etc/systemd/system/etcd.service

###########################
[Unit]
Description=etcd key-value store
Documentation=https://github.com/etcd-io/etcd
After=network.target

[Service]
User=etcd
Type=notify
Environment=ETCD_DATA_DIR=/var/lib/etcd
Environment=ETCD_NAME=%m
ExecStart=/usr/local/bin/etcd
Restart=always
RestartSec=10s
LimitNOFILE=40000

[Install]
WantedBy=multi-user.target

################################

sudo systemctl  daemon-reload

sudo systemctl  start etcd.service

sudo systemctl enable etcd.service

systemctl status etcd.service

 ss -tunelp | grep 2379
 
 etcdctl member list
 
 etcdctl  endpoint health
