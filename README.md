# Running a Chainlink node!

2 cores, 4GB RAM, and 16GB storage is recommended.

Ensure that your system is up to date and fully patched

Debian & Ubuntu

```shell
sudo apt update -y && sudo apt upgrade -y
```

CentOS

```shell
sudo yum update && sudo yum upgrade -y
```



Install what we can from the default repository:

Debian & Ubuntu

```shell
sudo apt -y install git build-essential net-tools vim apt-transport-https ca-certificates curl wget gnupg2 software-properties-common ntp screen postgresql
```

CentOS

```shell
sudo yum -y install git gcc gcc-c++ make openssl-devel net-tools vim ca-certificates curl wget gnupg2 ntp screen postgresql-server postgresql-contrib yum-utils
```

Start the ntp service

Debian & Ubuntu

```shell
sudo /etc/init.d/ntp start
```

CentOS

```shell
sudo systemctl start ntpd
```

## Installing Docker

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

(Optional) Start using screen

```shell
screen
```
### If using a separate machine for your Ethereum node, skip to the PostgreSQL section

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

## Install Ethereum locally (geth)

```shell
git clone https://github.com/ethereum/go-ethereum.git && cd go-ethereum
make geth
./build/bin/geth --rpc --rpcaddr 172.17.0.1 --syncmode "light"
```

Running geth with the `--fast` option will also work instead of `--syncmode "light"`, but you can't do both

Send it to the background with <kbd>Ctrl</kbd>+<kbd>Z</kbd> or create a new screen with <kbd>Ctrl</kbd>+<kbd>A</kbd>, <kbd>C</kbd>
 
## Set up PostgreSQL (Debian & Ubuntu)

Replace 9.6 with your version number. You can find this with "ls /etc/postgresql"

```shell
cd /etc/postgresql/9.6/main/
```

If you're setting up the node with different IPs, change them here

```shell
sudo sh -c "echo \"listen_addresses = '172.17.0.1'\" >> postgresql.conf"
sudo sh -c "echo \"host all all 172.17.0.0/16 trust\" >> pg_hba.conf"
```

Restart PostgreSQL

```shell 
sudo /etc/init.d/postgresql restart
```

Start PostgreSQL at boot

```shell
sudo update-rc.d postgresql enable
```

## Set up PostgreSQL (CentOS)

Init the database

```shell
sudo postgresql-setup initdb
```

If you're setting up the node with different IPs, change them here

```shell
sudo su postgres 
cd /var/lib/pgsql/data
sh -c "echo \"listen_addresses = '172.17.0.1'\" >> postgresql.conf"
sh -c "echo \"host all all 172.17.0.0/16 trust\" >> pg_hba.conf"
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

## Finally set up Chainlink:

```shell
cd
docker pull smartcontract/chainlink
```

### Create a .env file

```shell
touch .env
```

You can also use POSTGRES_USER and POSTGRES_PASSWORD environment variables in the .env file if you set up a different user in PostgreSQL

```shell
echo "DATABASE_URL=postgresql://postgres@172.17.0.1:5432/nayru_development?encoding=utf8&pool=5&timeout=5000" >> .env
echo "ETHEREUM_URL=http://172.17.0.1:8545" >> .env
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

Assignment creations use port 5100 by default, you could also specify a port using `-p ####:####` in the command above to expose another port.

Test connection (should be up to date with current Ethereum block):

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
