# ChainLink Node Troubleshooting

## Common terms

* `GETH_NODE_IP` for the machine running Geth
* `POSTGRES_IP` for the machine running PostgreSQL
* `CHAINLINK_IP` for the machine running the ChainLink node

### Check syncing status via RPC

```shell
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"eth_syncing","params":[],"id":1}' http://GETH_NODE_IP:8545
```
