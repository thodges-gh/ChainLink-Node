# Running a Chainlink node!

Spin up a VM running Debian Stretch (9). 2 cores, 4GB RAM, and 16GB storage is recommended.

Ensure that your system is up to date and fully patched

```script
sudo apt update -y && sudo apt upgrade -y
```

Install what we can from the default repository:

```shell
sudo apt -y install git build-essential net-tools vim apt-transport-https ca-certificates curl wget gnupg2 software-properties-common ntp screen postgresql
```

Start the ntp service

```shell
sudo /etc/init.d/ntp start
```

Start using screen

```shell
screen
```

## Installing Docker:

```shell
curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg | sudo apt-key add -
sudo add-apt-repository -y "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") $(lsb_release -cs) stable"
sudo apt -y update
sudo apt -y install docker-ce

# Allows use of docker commands without sudo
sudo gpasswd -a $USER docker
su $USER
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
echo "export PATH=$PATH:/usr/local/go/bin" >> ~/.profile
source ~/.profile
```

## Install Ethereum locally (geth)

```shell
git clone https://github.com/ethereum/go-ethereum.git && cd go-ethereum
make geth
./build/bin/geth --rpc --rpcaddr 172.17.0.1 --syncmode "light"
```

Create a new screen

```shell
Ctrl a, c
```

Create a new wallet in Geth

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

Create a new screen

```shell
Ctrl a, c
```
 
## Set up PostgreSQL

When setting up PostgreSQL we need to allow connections from docker images.

First, find the network interface used by Docker by running:

```shell
sudo ifconfig
```

This will write out two columns, the interface name on the left and a block of text on the right. Look for the interface named `docker0`.

The output should look something like this:

```shell
$ sudo ifconfig
docker0   Link encap:Ethernet  HWaddr 02:42:0a:6a:da:ea  
          inet addr:172.17.0.1  Bcast:0.0.0.0  Mask:255.255.0.0
          inet6 addr: fe80::42:aff:fe6a:daea/64 Scope:Link
          [...]
```

Write down the IP shown after `inet addr:`. It may not be the same as the example above, but should be an IPv4 address.

Next, add the settings to the PostgreSQL configuration files. Replace the example IP below with the IP you found above.

```shell
# Replace 9.6 with your version number. You can find this with "ls /etc/postgresql"
cd /etc/postgresql/9.6/main/

# Add settings by running these commands or manually appending the setting lines to the files
sudo sh -c "echo \"listen_addresses = '172.17.0.1'\" >> postgresql.conf"
# Remember to replace the IP with your Docker IP
sudo sh -c "echo \"host all all 172.17.0.0/16 trust\" >> pg_hba.conf"

# Restart PostgreSQL
sudo /etc/init.d/postgresql restart
```

## (Optional) Open up port 8545 & 5432

```shell
sudo iptables -A INPUT -p tcp -s 0/0 --sport 1024:65535 -d 172.17.0.1  --dport 5432 -m state --state NEW,ESTABLISHED -j ACCEPT
sudo iptables -A OUTPUT -p tcp -s 172.17.0.1 --sport 5432 -d 0/0 --dport 1024:65535 -m state --state ESTABLISHED -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 8545 --jump ACCEPT
sudo iptables-save
```

## Finally set up Chainlink:

```shell
cd
docker pull smartcontract/chainlink
```

### Create a .env file

```shell
vim .env
```
You can also use POSTGRES_USER and POSTGRES_PASSWORD environment variables in the .env file if you set up a different user in PostgreSQL

Use this text as example, but change the IP to your docker0 interface address if it's different:

```shell
DATABASE_URL=postgresql://postgres@172.17.0.1:5432/nayru_development?encoding=utf8&pool=5&timeout=5000
ETHEREUM_URL=http://172.17.0.1:8545
ETHEREUM_EXPLORER_URL=https://etherscan.io
```

Run this to initialize the database:

```shell
docker run -it --env-file=.env smartcontract/chainlink rake oracle:initialize
```

It will ask if you're ready to print coordinator credentials to the screen. You need to actually type "Y" for it to print out the coordinators. Copy them into a text file.

And finally run this to actually start the node:

```shell
docker run -t --env-file=.env smartcontract/smartoracle
```

Test connection:

```shell
docker run -it --env-file=.env smartcontract/chainlink rails runner "puts Ethereum::Client.new.current_block_height"
```

### Stopping the node

If you want to stop the ChainLink node, you need to kill the entire docker container.

First, get a list of running docker containers with:

```shell
docker ps
```

Look for the line where "image" is set to `smartcontract/smartoracle`.
Copy the container id and use it in the following command: (e.g. `docker kill 23e27b5e63fb`)

```shell
docker kill CONTAINER_ID
```
