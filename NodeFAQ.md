# ChainLink Node FAQ

## What are the hardware requirements for running a node?

At a minimum, a ChainLink node which is also running Geth (as a light client) should have:
* 2 Cores
* 4 GB RAM
* 16 GB Storage

As we get closer to when the node network goes live, we'll be able to tell how adding hardware resources scales for a node's ability to complete more assignments.

---

## How much LINK is required to be a node operator?

No LINK is required in order to be a node operator. However, holding LINK on your node (commonly referred to as staking) helps with ranking your node against others. Your node will also need some LINK in order to bid on smart contracts with the penalty amount. If the node is not selected to perform the job, or the node successfully completes the job, then it can retrieve that paid penalty amount.

---

## Wouldn’t holding millions of LINK automatically rank you to the top above the other nodes?

No, holding LINK is just one of the factors when determining a node’s ranking. Having LINK on a node helps get the node started, but there is a point of diminishing returns for how much LINK to hold.

---

## What are the factors for ranking a node?

* Availability (uptime)
* Correctness of responses
* Total number of assigned requests
* Total number of completed requests
* Total number of accepted requests
* Average time to respond
* Amount of penalty payments
* Amount of LINK held

---

## How active do I need to be with running a node?

There are a few manual tasks that need to be accomplished. As a node operator, you will need to establish connections to different API endpoints which would feed data back to a smart contract. You will also need to ensure that the system is well-maintained and updated with the latest security patches.

---

## Is there a reward system for running a node?

You'll set your own prices (paid in LINK) for retriving data from 3rd party sources. It is not yet known how the [node operator incentive fund](https://etherscan.io/address/0x98c63b7b319dfbdf3d811530f2ab9dfe4983af9d) will be used. So for now, the prices which you set on your node will be the only known form of income to node operators.

---

## What is the process for how the LINK token is used?

When a user creates a smart contract, they'll be able to put a price (in LINK) on how much they're willing to pay for the data retrieval. Nodes will bid on those smart contracts by paying the penalty fee (in LINK) if the smart contract creator has decided to set an amount for the penalty fee. Nodes not selected will be able to withdraw their LINK that they spent on bidding. Nodes that were selected will be able to withdraw their penalty fee and rewards for data retrieval after they have submitted data which was accepted by the smart contract.

As the node returns acceptable data to the smart contract, it will be paid (in LINK) for both partial and whole (whichever is relevant) completion. For example, if an assignment is to retrieve a data point from a data provider once, then upon completion, the node would be paid for 100% of the assignment. Another example, if an assignment is to retrieve a data point from a provider every day for 30 days, then each time the node returns acceptable data to the smart contract, it will be paid as a percentage of the total assignment.

Payment for assignment completion and penalty withdrawals accumulate over time, instead of automatically being released back to the node's Ethereum address. The node operator will need to pay the gas fee in order to withdraw the accumulated LINK.

---

## Does the ChainLink node need to be ran on the same machine as my Ethereum node?

No, as long as you are running your Ethereum node with the --rpc flag and a port opened, you can configure the .env file to connect to a remote Ethereum node. Set the ETHEREUM_URL value to your remote Ethereum node and port.

---

## Does Ethereum need to run in --full mode?

No, it's not necessary to download the entire blockchain history in order to run a ChainLink node. You're free to use either --fast or --syncmode "light" options when running geth, just make sure you also use the --rpc flag.
