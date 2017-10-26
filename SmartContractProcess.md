# ChainLink’s Smart Contract Process

1.	Oracle Selection

    *	User creates requirements specification (SLA)

        * Query parameters

        * Number of oracles
        
        * Reputation
        
        * Penalty amount

        * Aggregating contracts

    *	Submit SLA to Order-Matching contract

        * ChainLink nodes place bid

            *	Nodes pay the penalty amount to bid

        * Contract accepts bids based on SLA requirements

    *	Nodes not chosen are refunded the penalty amount

2.	Data Reporting

    *	Nodes connect to external data sources via their API endpoints
    
    * Node processes received data through adapters
    
        * Data is parsed for the blockchain

    *	Node digitally signs its response

    *	Nodes send a hash of response plus a nonce (commitment)

3.	Result Aggregation

    *	Nodes reveal results to oracle contract with their nonce

        * Oracle contract tallies results

        * Oracle contract calculates a weighted answer

    *	Validity of oracle responses are reported to the reputation contract
    
        * Responses are checked against their commitments from the Data Reporting phase
        
        * Responses that don't match their commitment are discarded

    *	Answer is returned to the user-created smart contract

## Outside the process

### Retrieving LINK

* Penalty payments may be withdrawn upon giving accepted answers to the aggregation contract

* Payments for providing data to the user's smart contract may also be withdrawn after returning accepted answers

* Withdrawing penalty payments and normal payments for retriving data incur a transaction fee (gas) so it is not automatic
