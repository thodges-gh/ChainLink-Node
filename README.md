# Running a Chainlink node!

Spin up a VM running Debian Stretch (9). At least 2GB RAM and 16GB storage is recommended.

Ensure that your system is up to date and fully patched

```script
sudo apt update -y && sudo apt upgrade -y
```

Install what we can from the default repository:

```shell
sudo apt -y install git build-essential net-tools vim apt-transport-https ca-certificates curl wget gnupg2 software-properties-common ntp postgresql
```

Start the ntp service

```shell
sudo /etc/init.d/ntp start
```

### Set up Go 1.9.1

Download and install Go:

```shell
curl -O https://storage.googleapis.com/golang/go1.9.1.linux-amd64.tar.gz
tar -xvf go1.9.1.linux-amd64.tar.gz
sudo mv go /usr/local
```

Set the Go paths:

```shell
sudo echo "export PATH=$PATH:/usr/local/go/bin" >> ~/.profile
source ~/.profile
```

## Install Ethereum locally (geth)

Change 192.168.2.76 to your IP address

```shell
git clone https://github.com/ethereum/go-ethereum.git && cd go-ethereum
make geth
./build/bin/geth --rpc --rpcaddr 192.168.2.76 --syncmode "light"
```

If you get an error about go version when running `make geth`, see install Go 1.9.1 instructions above.

Open a new terminal or screen

```shell
cd go-ethereum/build/bin
./geth account new
```

Enter in a password and confirm

```shell
./geth account list
```

You should see the account that you just created

```shell
./geth attach
net.listening
net.peerCount
```

Just let that run and open a new terminal or screen to do the rest

## Installing docker:

```shell
curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg | sudo apt-key add -
sudo add-apt-repository -y "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") $(lsb_release -cs) stable"
sudo apt -y update
sudo apt -y install docker-ce
```
 
## Set up PostgreSQL

```shell
# Replace 9.6 with your version number if the directory doesn't exist. E.g. 9.5 on Ubuntu
cd /etc/postgresql/9.6/main/

# Add settings
echo "listen_addresses = '*'" >> postgresql.conf
echo "host  all  all 0.0.0.0/0 trust" >> pg_hba.conf

# Restart PostgreSQL
sudo /etc/init.d/postgresql restart
```

## Open up port 8545

```shell
sudo iptables -A INPUT -p tcp --dport 8545 --jump ACCEPT
sudo iptables-save
```

## Finally set up Chainlink:

```shell
cd
sudo docker pull smartcontract/chainlink
```

Note the IP address for your host machine

```shell
sudo ifconfig
```

### Create a .env file

```shell
vim .env
```

Use this text as example, but change the IP to your non-loopback-IP-address:

```shell
DATABASE_URL=postgresql://postgres@10.0.2.15:5432/nayru_development?encoding=utf8&pool=5&timeout=5000
EMAIL_USERNAME=sender@example.com
EMAIL_PASSWORD=password
ETHEREUM_URL=http://10.0.2.15:8545
ETHEREUM_EXPLORER_URL=https://testnet.etherscan.io
NOTIFICATION_EMAIL=receiver@exmaple.com
```

Run this to initialize the database:

```shell
sudo docker run -it --env-file=.env smartcontract/chainlink rake oracle:initialize
```

It will ask if you're ready to print coordinator credentials to the screen. You need to actually type "Y" for it to print out the coordinators. Copy them into a text file.

And finally run this to actually start the node:

```shell
sudo docker run -t --env-file=.env smartcontract/smartoracle
```

Test connection:

```shell
sudo docker run -it --env-file=.env smartcontract/chainlink rails runner "puts Ethereum::Client.new.current_block_height"
```

### Stopping the node

If you want to stop the ChainLink node, you need to kill the entire docker container.

First, get a list of running docker containers with:

```shell
sudo docker ps
```

Look for the line where "image" is set to `smartcontract/smartoracle`.
Copy the container id and use it in the following command: (e.g. `docker kill 23e27b5e63fb`)

```shell
sudo docker kill CONTAINER_ID
```
