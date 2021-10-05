# Indexer Event Processing Interface

This document describes event processing and registration interface for WASM indexers.

## Event Registration Format

The indexer will register a handler for events in the following format:

- `<graph-namespace>`: String, identifier for a WASM indexer and associated graphql types. This namespace will map
  to a list of objects containing the following:
  - `type`: The source of the event, e.g. Contract
  - `address`: address of contract generating the event, or null otherwise
  - `events`: list of event types this handler function is interested in
  - `handler`: string name of the wasm event handler

Example:

```json
{
  "my-indexer": [
    {
      "type": "Contract",
      "address": "0xFEEDBABEF00D",
      "events": [ "CallReceipt", "SomeEvent" ],
      "handler": "an_event_handler",
    },
    ...
  ]
}
```

The above will register a WASM indexer to listen for Contract events coming from `0xFEEDBABEF00D`, and pass `Call` and `Return` receipts to the handler named `an_event_handler`.

`an_event_handler` will then need a function signature containing two types: `CallReceipt` and `SomeEvent`. These events will be passed in to the handler function in a serialized format according to the [ABI](../protocol/abi.md)
