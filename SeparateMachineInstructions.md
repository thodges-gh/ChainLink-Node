# Separate ChainLink Component Install Guide

## All Machines

Ensure that your systems are up to date and fully patched

Debian & Ubuntu

```shell
sudo apt update -y && sudo apt upgrade -y
```

CentOS & Amazon Linux

```shell
sudo yum update && sudo yum upgrade -y
```

Take note of the IP addresses for each machine (run `ifconfig`), since the instructions can't assume what they will be. 

Fill in with the actual IP when required. These will be noted by:

 * `GETH_NODE_IP` for the machine running Geth
 * `POSTGRES_IP` for the machine running PostgreSQL
 * `CHAINLINK_IP` for the machine running the ChainLink node

## Geth Node

### Install required packages

Debian & Ubuntu

```shell
sudo apt -y install git build-essential net-tools vim curl ntp screen
```

CentOS & Amazon Linux

```shell
sudo yum -y install git gcc gcc-c++ make openssl-devel net-tools vim curl ntp screen yum-utils
```

Start the ntp service

Debian & Ubuntu

```shell
sudo /etc/init.d/ntp start
sudo systemctl enable ntp
```

CentOS

```shell
sudo systemctl start ntpd
sudo update-rc.d ntpd enable
```

Amazon Linux

```shell
sudo /etc/init.d/ntpd start
sudo chkconfig ntpd on
```

Set up swap file

```shell
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo su
echo '/swapfile none swap sw 0 0' >> /etc/fstab
exit
sudo /sbin/shutdown -r now
```

### Install Ethereum (Ubuntu)

```shell
sudo apt-get install software-properties-common
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get install ethereum
```

### Set up Go 1.9.2 (All others)

Download and install Go:

```shell
curl -O https://storage.googleapis.com/golang/go1.9.2.linux-amd64.tar.gz
tar -xvf go1.9.2.linux-amd64.tar.gz
sudo mv go /usr/local
```

Set the Go paths:

```shell
echo "export PATH=$PATH:/usr/local/go/bin" >> ~/.profile
source ~/.profile
```

### Install Ethereum locally (All others)

Replace `GETH_NODE_IP` with the IP of the machine running the Geth node

```shell
git clone https://github.com/ethereum/go-ethereum.git && cd go-ethereum
make geth
./build/bin/geth --fast --cache=16 --rpc --rpcaddr GETH_NODE_IP --ipcdisable
```

## PostgreSQL

### Install required packages

Debian & Ubuntu

```shell
sudo apt -y install net-tools vim screen postgresql
```

CentOS

```shell
sudo yum -y install net-tools vim screen postgresql-server postgresql-contrib yum-utils
```

Amazon Linux

```shell
sudo yum -y install net-tools vim screen postgresql96-server yum-utils
```

### Set up PostgreSQL (Debian & Ubuntu)

Replace 9.6 with your version number. You can find this with "ls /etc/postgresql"

```shell
cd /etc/postgresql/9.6/main/
```

If you're setting up the node with different IPs, change them here. Replace `CHAINLINK_IP` and `CHAINLINK_IP/##` with the IP of the machine running the ChainLink node. The `/##` should be the prefix for the subnet mask.

```shell
sudo sh -c "echo \"listen_addresses = 'CHAINLINK_IP'\" >> postgresql.conf"
sudo sh -c "echo \"host all all CHAINLINK_IP/## trust\" >> pg_hba.conf"
```

Start PostgreSQL

```shell 
sudo /etc/init.d/postgresql restart
sudo update-rc.d postgresql enable
```

### Set up PostgreSQL (CentOS)

Init the database

```shell
sudo postgresql-setup initdb
```

If you're setting up the node with different IPs, change them here. Replace `CHAINLINK_IP` and `CHAINLINK_IP/##` with the IP of the machine running the ChainLink node. The `/##` should be the prefix for the subnet mask.

```shell
sudo su postgres 
cd /var/lib/pgsql/data
sh -c "echo \"listen_addresses = 'CHAINLINK_IP'\" >> postgresql.conf"
sh -c "echo \"host all all CHAINLINK_IP/## trust\" >> pg_hba.conf"
exit
```

Start PostgreSQL

```shell
sudo systemctl start postgresql
sudo systemctl enable postgresql
```

## Set up PostgreSQL (Amazon Linux)

Init the database

```shell
sudo service postgresql initdb
```

If you're setting up the node with different IPs, change them here

```shell
sudo su postgres 
cd /var/lib/pgsql9/data
sh -c "echo \"listen_addresses = '172.17.0.1'\" >> postgresql.conf"
sh -c "echo \"host all all 172.17.0.0/16 trust\" >> pg_hba.conf"
exit
```

Start PostgreSQL

```shell 
sudo /etc/init.d/postgresql restart
sudo chkconfig docker on
```

## Chainlink core

### Install required packages

Debian & Ubuntu

```shell
sudo apt -y install net-tools vim apt-transport-https ca-certificates curl gnupg2 software-properties-common screen
```

CentOS

```shell
sudo yum -y install net-tools vim ca-certificates curl gnupg2 screen yum-utils
```

Amazon Linux

```shell
sudo yum -y install net-tools vim ca-certificates curl gnupg2 screen yum-utils
```

### Install Docker

Debian

```shell
curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg | sudo apt-key add -
sudo add-apt-repository -y "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") $(lsb_release -cs) stable"
sudo apt -y update
sudo apt -y install docker-ce
```

Ubuntu (17.04+, Artful doesn't have its own release yet, Zesty works fine)

```shell
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu zesty stable"
sudo apt -y update
sudo apt -y install docker-ce
```

CentOS

```shell
sudo yum-config-manager  --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum -y install docker-ce
sudo systemctl start docker
```

Amazon Linux

```shell
sudo yum -y install docker
sudo /etc/init.d/docker start
sudo chkconfig docker on
```

Allow use of Docker commands without sudo

```shell
sudo gpasswd -a $USER docker
su $USER
```

### Set up ChainLink

```shell
docker pull smartcontract/chainlink
```

### Create a .env file

```shell
touch .env
```

You can also use POSTGRES_USER and POSTGRES_PASSWORD environment variables in the .env file if you set up a different user in PostgreSQL. Replace `POSTGRES_IP` with the IP of the server running PostgreSQL and `GETH_NODE_IP` with the IP of the machine running the Geth node

```shell
echo "DATABASE_URL=postgresql://postgres@POSTGRES_IP:5432/nayru_development?encoding=utf8&pool=5&timeout=5000" >> .env
echo "ETHEREUM_URL=http://GETH_NODE_IP:8545" >> .env
echo "ETHEREUM_EXPLORER_URL=https://etherscan.io" >> .env
```

Before running the next line, please <mark>**change the password**</mark> to something that you prefer

```shell
echo "PRIVATE_KEY_PASSWORD=passwordchangeme" >> .env
```

Run this to initialize the database:

```shell
docker run -it --env-file=.env smartcontract/chainlink rake oracle:initialize
```

It will ask if you're ready to print coordinator credentials to the screen. You need to actually type "Y" for it to print out the coordinators. Copy them into a text file.

And finally run this to actually start the node:

```shell
docker run -t --env-file=.env smartcontract/chainlink
```
