# Ethereum Account on ChainLink

## Read this first

Please make sure that you have the `PRIVATE_KEY_PASSWORD` variable set within your .env file BEFORE running the `rake oracle:initialize` command to initialize the database.

If you ran the initialization command before making sure that your private key password was what you want it to be, you will need to drop the nayru_development database and reinitialize (this will change your coordinator keys).

## Install your node

Follow along the instructions [here](https://github.com/thodges-gh/ChainLink-Node) to get your node up and running.

## Create the keystore file

Connect to the database

```shell
sudo su postgres
psql nayru_development
```

Select the data

```SQL
SELECT encrypted_private_key FROM key_pairs WHERE owner_type = 'Ethereum::Account';
```

It should look like this:

```JSON
{"crypto":{"cipher":"aes-128-ctr","cipherparams":{"iv":"771873094bd263c5431c4378d7cd015e"},"ciphertext":"fa1664b9574da817a0f1bec2492586874e4091cbdc023d90daac172f1b83e89d","kdf":"pbkdf2","kdfparams":{"c":262144,"dklen":32,"prf":"hmac-sha256","salt":"3c31f12ce61b645a61eda11f76372a9c65380d9f346fc1033136143542a21a95"},"mac":"294a57a913c3eaea1ab1e8f599fc13f01bcb3966708cfaa06f0906ceea2df566"},"id":"ebecccc5-681b-4681-a12b-0fdeb2924c90","version":3,"address":"0xC7b1F27285325c9030d47B7Ce875A98ff76ae116"}
```

You can copy/paste this into a text file. This is what makes up a keystore file. You can now open it up with any application that uses keystore files with the password given to the `PRIVATE_KEY_PASSWORD` variable in the .env file.

## Questions / Issues

### I already initialized my database without a value set for PRIVATE_KEY_PASSWORD, what do I do?

Assuming you haven't actually sent LINK to the address that was created, you can simply drop the nayru_development database and run the initize command to recreate it with your password set.

Connect to PostgreSQL

```shell
sudo su postgres
psql
```

Drop the database

```SQL
DROP DATABASE nayru_development;
```

Exit out of the psql and the postgres user

```shell
\q
exit
```

Make sure your environment variables are set in the .env file

```shell
edit .env
```
Reinitialize the database

```shell
docker run -it --env-file=.env smartcontract/chainlink rake oracle:initialize
```

### What is the other address for in the key_pairs table?

That was used for testing in a previous release of the ChainLink software and will be deprecated in the future

### Will I also need to send ETH to the address for gas to move LINK?

Yes, you will need some ETH to be used for gas. The amount can vary depending on how many assignments the node will accept.

## Commands

### Obtain the node's public address

```shell
docker run -it --env-file=.env smartcontract/chainlink rails runner "puts Ethereum::Account.default.key_pair.ethereum_address"
```

### Obtain the node's associated private key


```shell
docker run -it --env-file=.env smartcontract/chainlink rails runner "puts Ethereum::Account.default.key_pair.private_key"
```
