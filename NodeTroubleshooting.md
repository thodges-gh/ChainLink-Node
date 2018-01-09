# ChainLink Node Troubleshooting

## Common terms

* `GETH_NODE_IP` for the machine running Geth
* `POSTGRES_IP` for the machine running PostgreSQL
* `CHAINLINK_IP` for the machine running the ChainLink node

### Check syncing status via RPC

```shell
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"eth_syncing","params":[],"id":1}' http://GETH_NODE_IP:8545
```

### Output frozen

```shell
02:48:39 clock.1  | I, [2018-01-09T02:48:39.846221 #6]  INFO -- : Triggering 'AssignmentScheduler.perform'
02:48:39 clock.1  | I, [2018-01-09T02:48:39.846432 #6]  INFO -- : Triggering 'Ethereum::ConfirmationWatcher.delay.perform'
02:48:39 clock.1  | I, [2018-01-09T02:48:39.846531 #6]  INFO -- : Triggering 'Ethereum::ContractConfirmer.delay.perform'
02:48:39 clock.1  | I, [2018-01-09T02:48:39.846588 #6]  INFO -- : Triggering 'TermJanitor.delay.clean_up'
02:48:39 clock.1  | I, [2018-01-09T02:48:39.846638 #6]  INFO -- : Triggering 'Assignment::Janitor.delay.schedule_clean_up'
02:49:39 clock.1  | I, [2018-01-09T02:49:39.861933 #6]  INFO -- : Triggering 'AssignmentScheduler.perform'
02:49:39 clock.1  | I, [2018-01-09T02:49:39.862826 #6]  INFO -- : Triggering 'Ethereum::ConfirmationWatcher.delay.perform'
02:49:39 clock.1  | I, [2018-01-09T02:49:39.863121 #6]  INFO -- : Triggering 'Ethereum::ContractConfirmer.delay.perform'
02:49:39 clock.1  | I, [2018-01-09T02:49:39.863452 #6]  INFO -- : Triggering 'TermJanitor.delay.clean_up'
02:49:39 clock.1  | I, [2018-01-09T02:49:39.863752 #6]  INFO -- : Triggering 'Assignment::Janitor.delay.schedule_clean_up'
```

If the output from the ChainLink node looks like the above and is no longer updating, check your connection to the database. The connection may be interrupted or the database may have gone down. Once restored, the ChainLink node Docker image will need to be restarted.
