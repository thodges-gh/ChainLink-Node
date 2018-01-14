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
curl -u $MY_KEY:$MY_PRIVATE -X POST -H 'Content-Type: application/json' -d '{"assignment":{"title":"Test Assignment 1","subtasks":[{"adapterType":"httpGetJSON","adapterParams":{"endpoint":"https://bitstamp.net/api/ticker/","fields":["last"]}}],"schedule":{"startAt":"now","endAt":"1478028219","hour":"0","minute":"0"}},"version":"1.0.0"}' http://172.17.0.2:5100/assignments
```

Assignment creations use port 5100 by default, you could also specify a port using `-p ####:####` in the command above to expose another port. The official ChainLink docs reference port 6688.

You'll get a JSON response, take note of the "xid" value and replace the x's  with it (leave the dashes)

```JSON
{
    "assignmentHash": "1824f1be161b0b0474f960c39d437da90b08d233a1a3ac944c109be41411a954",
    "signature": "1c114d1a04cd6a60f30333b90e4c7a46a3812783ca0d212388cef4b1c58399f10452edc678653f047b6ad1cc068492e7647dbec11b79cb030037a948ce7ce37b58",
    "xid": "55b0678d-0c00-4a5a-9dd3-3a79f397ce68"
}
```

Show the assignment

```shell
curl -u $MY_KEY:$MY_PRIVATE http://172.17.0.4:5100/assignments/55b0678d-0c00-4a5a-9dd3-3a79f397ce68
```

The output here will be in JSON format, and contains the details of the assignment

```JSON
{
    "adapterType": "JsonAdapter",
    "adapterTypes": [
        "JsonAdapter"
    ],
    "endAt": "1478028219",
    "parameters": {
        "endpoint": "https://bitstamp.net/api/ticker/",
        "fields": [
            "last"
        ]
    },
    "subtasks": [
        null
    ],
    "startAt": "0",
    "status": "completed",
    "xid": "55b0678d-0c00-4a5a-9dd3-3a79f397ce68",
    "snapshots": [
        {
            "assignmentXID": "55b0678d-0c00-4a5a-9dd3-3a79f397ce68",
            "description": null,
            "descriptionURL": null,
            "details": {
                "value": "13331.98"
            },
            "status": null,
            "summary": "The parsed JSON returned \"13331.98\".",
            "value": "13331.98",
            "xid": "aaf61cc1-87f3-4302-b22a-80fe15c06491"
        },
        {
            "assignmentXID": "55b0678d-0c00-4a5a-9dd3-3a79f397ce68",
            "description": null,
            "descriptionURL": null,
            "details": null,
            "status": "completed",
            "summary": "Assignment \"55b0678d-0c00-4a5a-9dd3-3a79f397ce68\" is completed.",
            "value": null,
            "xid": "8bcf5941-713a-4832-bb03-f7a81c74c2eb"
        }
    ]
}
```

You can also show the specific snapshot details by passing in the snapshot's xid

```shell
curl -u $MY_KEY:$MY_PRIVATE http://172.17.0.4:5100/snapshots/8bcf5941-713a-4832-bb03-f7a81c74c2eb
```

Which returns

```JSON
{
    "assignmentXID": "55b0678d-0c00-4a5a-9dd3-3a79f397ce68",
    "description": null,
    "descriptionURL": null,
    "details": null,
    "status": "completed",
    "summary": "Assignment \"55b0678d-0c00-4a5a-9dd3-3a79f397ce68\" is completed.",
    "value": null,
    "xid": "8bcf5941-713a-4832-bb03-f7a81c74c2eb"
}
```
