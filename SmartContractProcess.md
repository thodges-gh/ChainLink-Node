# ChainLink’s Smart Contract Process

1.	Oracle Selection

    *	User creates requirements specification (SLA)

        *	Query parameters

        *	Number of oracles
        
        *	Reputation
        
        *	Penalty amount

        *	Aggregating contracts

    *	Submit SLA to Order-Matching contract

        *	ChainLink nodes place bid

            *	Nodes pay the penalty amount to bid

        *	Contract accepts bids based on SLA requirements

    *	Nodes not chosen are refunded the penalty amount

2.	Data Reporting

    *	Nodes connect to external API endpoints via adapters

    *	Oracle digitally signs its response

    *	Results are returned to the aggregation contract

3.	Result Aggregation

    *	Oracles reveal results to oracle contract

        *	Oracle contract tallies results

        *	Oracle contract calculates a weighted answer

    *	Validity of oracle responses are reported to the reputation contract

    *	Answer is returned to the user-created smart contract
