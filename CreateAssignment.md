# Creating an Assignment

Make sure you have the latest build

```shell
docker pull smartcontract/chainlink
```

Set your coordinators to environment variables (replace the x's with your actual coordinators)

```shell
echo "export MY_KEY=\"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\"" >> ~/.bashrc
echo "export MY_PRIVATE=\"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\"" >> ~/.bashrc
source ~/.bashrc
```

Make sure the following environment variables are defined in your ~/.env file

```shell
ETHEREUM_GAS_PRICE_MINIMUM=0.000000001
```

Create the assignment

```shell
curl -u $MY_KEY:$MY_PRIVATE -X POST -H 'Content-Type: application/json' -d '{"assignment":{"title":"Test Assignment 1","subtasks":[{"adapterType":"httpGetJSON","adapterParams":{"endpoint":"https://bitstamp.net/api/ticker/","fields":["last"]}},{"adapterType":"ethereumBytes32"}],"schedule":{"startAt":"now","endAt":"1478028219","hour":"0","minute":"0"}},"version":"1.0.0"}' http://172.17.0.2:5100/assignments
```

You'll get a JSON response, take note of the "xid" value and replace the x's  with it (leave the dashes)

```shell
curl -u $MY_KEY:$MY_PRIVATE http://172.17.0.2:5100/assignments/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

The output here will be in JSON format, and contains the details of the assignment
