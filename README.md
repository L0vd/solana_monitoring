# Solana monitoring by L0vd
## Community dashboard by L0vd.com: [Dashboard link](http://213.246.45.190:3000/d/f2b2HcaGz/solana-community-validator-dashboard?orgId=1&refresh=1m)

Install telegraf
```
sudo apt update
sudo apt -y install curl jq bc
```
```
# install telegraf
sudo cat <<EOF | sudo tee /etc/apt/sources.list.d/influxdata.list
deb https://repos.influxdata.com/ubuntu bionic stable
EOF
sudo curl -sL https://repos.influxdata.com/influxdb.key | sudo apt-key add -

sudo apt update
sudo apt -y install telegraf
```
```
sudo systemctl enable --now telegraf
sudo systemctl is-enabled telegraf

# make the telegraf user sudo and adm to be able to execute scripts as haqq user
sudo adduser telegraf sudo
sudo adduser telegraf adm
sudo -- bash -c 'echo "telegraf ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers'
```
You can check telegraf service status:
```
sudo systemctl status telegraf
```
Status can be not ok with default Telegraf's config. Next steps will fix it.

Clone this project repo 
```
cd $HOME/solana
git clone https://github.com/L0vd/solana_monitoring.git
cd $HOME/solana/solana_monitoring
chmod +x monitor.sh
```

Edit telegraf configuration and CHANGE YOUR MONIKER!
```
sudo mv /etc/telegraf/telegraf.conf /etc/telegraf/telegraf.conf.orig
sudo mv telegraf.conf /etc/telegraf/telegraf.conf
sudo nano /etc/telegraf/telegraf.conf
```

Set you name to identify yourself in grafana dashboard and check correctness of the path to your monitor.sh file and username your validator runs at. Correct these two settings in the configuration file:
```
# Global Agent Configuration
[agent]
  hostname = "YOUR_MONIKER/SERVER_NAME" # set this to a name you want to identify your node in the grafana dashboard
...
...
[[inputs.exec]]
  commands = ["sudo su -c /root/solana/solana_monitoring/monitor.sh -s /bin/bash root"] # change path to your monitor.sh file and username to the one that validator runs at (e.g. root)
  interval = "1m"
  timeout = "1m"
  data_format = "influx"
  data_type = "integer"
```
Restart telegraf and check if everything is ok
```
systemctl daemon-reload
systemctl restart telegraf
journalctl -u telegraf -f
```
