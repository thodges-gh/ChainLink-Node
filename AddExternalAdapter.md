# Adding an External Adapter to the ChainLink Node

This guide shows how to add an external adapter to the ChainLink core software via the Rails console.

## Running the Rails Console

```shell
docker run -it --env-file=.env smartcontract/chainlink rails c
```

## Create an Assignment Type

An assignment type will be used to determine the data that will be provided to smart contracts. This also makes it so that multiple adapters can utilize the same assignment types so long as they return JSON data which conforms to the assignment type's schema.

```ruby
testAT = AssignmentType.create(name: "testAssignmentType", description: "this is a test assignment type", json_schema: "{}")
```

## Create the Adapter

The URL will be where the adapter is running. If required, a username and password can be supplied. If the username and password is left blank, one will be generated for you.

```ruby
ea = ExternalAdapter.create(url: "http://localhost:8000", assignment_type: testAT)
# or
ea = ExternalAdapter.create(url: "http://localhost:8000", assignment_type: testAT, username: "username1", password: "password1")
```

## Updating the Adapter's Information

If needed, you can update the information for the external adapter by using the `update_attributes` method.

```ruby
ea.username # => "username1"
ea.update_attributes(username: "username2")
ea.username # => "username2"
```

## Finding an Adapter on the Node

Searching for external adapters on the node can be done with typical Ruby methods.

```ruby
ExternalAdapter.first
ExternalAdapter.find(1) #uses the id field
```
