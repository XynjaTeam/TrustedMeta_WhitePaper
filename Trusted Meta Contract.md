# Trusted Meta Contract

## Method List

**registerClient**
Register a contract to be served by the trusted-meta.

**getClients**
Get the list of registered clients.

**topUpClient**
Add to the fund for a specific client.

**getClientStatus**
Get total received fund, total spent fund, transaction count in the past day, number of active trusted meta for a specific client.

**getTopUpStatus**
Get record of the 50 most recent top ups for a specific client.

**registerTrustedMeta**
Register a trusted meta to serve a specific client.

**unregisterTrustedMeta**
Unregister a trusted meta from serving a specific client.

**getTrustedMetaStatus**
Get the sum of the gathered prize for a trusted meta and the same per client that the trusted meta is registered for.

**claimPrize**
Called by a trusteed meta to claim their prize.

**lookUpTrustedMeta**
Get a number of weighted random list of trusted meta for a specific client.

**callFunction**
Called by a trusted meta to call a function of a client.

**getCallResult**
Returns the result of a previous function call.

## registerClient

