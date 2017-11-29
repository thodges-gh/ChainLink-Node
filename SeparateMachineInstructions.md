# Separate ChainLink Component Install Guide

## All Machines

Ensure that your systems are up to date and fully patched

Debian & Ubuntu

```shell
sudo apt update -y && sudo apt upgrade -y
```

CentOS

```shell
sudo yum update && sudo yum upgrade -y
```

Take note of the IP addresses for each machine, since the instructions can't assume what they will be. 

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

CentOS

```shell
sudo yum -y install git gcc gcc-c++ make openssl-devel net-tools vim curl ntp screen yum-utils
```

### Set up Go 1.9.2

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

### Install Ethereum locally (geth)

```shell
git clone https://github.com/ethereum/go-ethereum.git && cd go-ethereum
make geth
./build/bin/geth --rpc --rpcaddr GETH_NODE_IP --syncmode "light"
```

Running geth with the `--fast` option will also work instead of `--syncmode "light"`, but you can't do both

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

### Set up PostgreSQL (Debian & Ubuntu)

Replace 9.6 with your version number. You can find this with "ls /etc/postgresql"

```shell
cd /etc/postgresql/9.6/main/
```

If you're setting up the node with different IPs, change them here

```shell
sudo sh -c "echo \"listen_addresses = 'CHAINLINK_IP'\" >> postgresql.conf"
sudo sh -c "echo \"host all all CHAINLINK_IP/## trust\" >> pg_hba.conf"
```

Restart PostgreSQL

```shell 
sudo /etc/init.d/postgresql restart
```

Start PostgreSQL at boot

```shell
sudo update-rc.d postgresql enable
```

### Set up PostgreSQL (CentOS)

Init the database

```shell
sudo postgresql-setup initdb
```

If you're setting up the node with different IPs, change them here

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
```

Start PostgreSQL at boot

```shell
sudo systemctl enable postgresql
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

You can also use POSTGRES_USER and POSTGRES_PASSWORD environment variables in the .env file if you set up a different user in PostgreSQL

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
