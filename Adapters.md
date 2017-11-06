# Adapters

## What is an Adapter

* They are used to process work in a sub task (off-chain, within the ChainLink node)
* They can be used to communicate with external services
* Two categories of adapters: core and external
* Utilize a minimal REST API

## Core Adapters
* **bitcoinComparisonJSON** Returns a signed Bitcoin transaction. Signs either a completion transaction or a failure transaction based on a value comparison of the input.
* **ethereumBytes32** Formats the input as Ethereum bytes32 value and writes it into the specified contract. Returns the unformatted value that was provided as input.
* **ethereumInt256** Formats the input as Ethereum int256 value and writes it into the specified contract. Returns the unformatted value that was provided as input.
* **ethereumUint256** Formats the input as Ethereum uint256 value and writes it into the specified contract. Returns the unformatted value that was provided as input.
* **ethereumFormatted** Writes a preformatted Ethereum hexadecimal value into the blockchain as configured. Returns the preformatted value that was provided as input.
* **ethereumLogWatcher** Returns the data field of an Ethereum event log, if one is provided. Otherwise, returns the value that was provided as the input. (Requires WeiWatchers integration.)
* **httpGetJSON** Retrieves JSON and returns the specific field selected in the configuration.
* **jsonReceiver** Generates a URL for the oracle to receive JSON push notifications. Parses the pushed JSON and returns the specific field selected in the configuration.

## External Adapters
* Can be written in any language as long as they return data conforming to the oracle schema
* Expand functionality of services offered by the oracle
* Can take input (from a separate API) and provide output (either to another adapter or to the core ChainLink software)
* URLs can be provided to specify where to parse data from
* Data can be passed into an adapter (including results from previous sub tasks)

## Adapter Workflow
* Perform request to an external API
* Process response and either:
* Return to the core (ChainLink node software), or
* Pass value to the next adapter

## Future changes planned for adapters
* Allow for adapters to pull data from sub tasks other than the immediate predecessor
* Allow for sub task pipeline to fan out and in to allow for sub tasks to rely on multiple processes
* Create a DSL to make specifications simpler and more composable

## Sources
* https://github.com/smartcontractkit/chainlink#adapters
* https://github.com/smartcontractkit/schemas#assignments-and-adapters
* https://chainlink-docs.smartcontract.com/#external-adapters
* https://link.smartcontract.com/whitepaper
