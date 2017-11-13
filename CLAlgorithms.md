# Breaking down ChainLink's algorithms

## Algorithm 1: In-contract Aggregation

> Page 14 of the white paper

1. Wait until a query is received from the user-created smart contract
2. Select nodes from the set of all valid session IDs
3. Broadcast the query to the selected nodes
4. Wait until commit messages are received from enough oracles to account for faulty nodes
5. Broadcast that enough messages were received
6. Wait until decommitments are received from enough oracles to account for honest nodes
7. Send the answer along with the nodes that provided it to the user-created smart contract

## Algorithm 2: Distributed Oracle

> Page 35 of the white paper

1. Receive a partial secret key
2. Receive individual aggregate answer from the data source
3. Create a partial signature with the partial secret key and answer
4. Broadcast the partial signature with the answer and session ID
5. Wait until enough nodes have commited their answers
6. Send commitments to provider smart contract
7. Broadcast the prepared message request
8. Wait until enough prepared messages have been received
9. Broadcast that enough messages were received
10. Wait until decommitments are received from enough oracles to account for honest nodes
11. If no valid whole signature has been received by the on-chain smart contract
    1. Aggregate the received decommitments into a valid signature
    2. Send the valid signatures to the on-chain smart contract
    3. Send the decommitments to the provider smart contract

## Algorithm 3: Reward Oracles

> Page 36 of the white paper

1. Wait until enough nodes have commited their answers and decommitment messages are received
2. Send reward to oracles included in the accepted decommitment set
