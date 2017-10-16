# Running a Chainlink node!
Spin up a VM running Debian Stretch (9)

Install what we can from the default repository:
```shell
sudo apt install git build-essential golang-go net-tools vim apt-transport-https ca-certificates curl wget gnupg2 software-properties-common ntp postgresql-9.6
```
Start the ntp service
```shell
sudo /etc/init.d/ntp start
```

## Install Ethereum locally (geth)
```shell
git clone https://github.com/ethereum/go-ethereum.git
cd go-ethereum
make geth
./build/bin/geth --rpc
```
Just let that run and open a new terminal to do the rest

## Installing docker:
```shell
curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") $(lsb_release -cs) stable"
sudo apt update
sudo apt install docker-ce
```
 
## Set up PostgreSQL

```shell
cd /etc/postgresql/9.6/main/
sudo vim postgresql.conf
```
Add this line:
```shell
listen_addresses = '*'
```
Then run:
```shell
sudo vim pg_hba.conf
```

Add this line:
```shell
host  all  all 0.0.0.0/0 trust
```
Then run:
```shell
sudo /etc/init.d/postgresql restart
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
