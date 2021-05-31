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

**getTopDonors**
Get the information of the top 111 donors.

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

### Inputs

- Client contract address
- Name 
- Description
- Owner identity (Can be a web URL or anything)
- Initial fund (payable function)

### Outputs

- The created client info

### Process

The initial fund is checked to be higher than a minimum. The minimum protects us against spam client generation. Basic validation goes through and a new client is created. The clients are stored with their contract address as the key.



## getClients

### Outputs

- The list of registered clients



## topUpClient

### Inputs

- Client contract address
- Donor name
- Donor identity (Can be a web URL or anything)
- Comment
- Paid amount (`amount`) (payable function)

### Outputs

- The new fund of the contract
- Total donations done by this donor
- The donor's total donation rank ranging from 0 to 110 inclusive (-1 if not in the top 111)

### Process

1. Add the received fund to the total fund of the client.
2. Create a donation object. Also note the current time in the object.
3. Add the donation to the list of donations for this client.
4. Use keccak256 and the donor identity to generate a unique key for this donor. If identity is not provided, it is an anonymous donor. The key will be zero.
5. Use the key to get the total donation amount of this donor. If a record doesn't exist, create it using the donation object.
6. Add the new donation to their total donation amount.
7. Use a leaderboard library with the key and the donor's total donation to get the ranking of this donation.
8. Return the new fund and the total donations done by this donor and the rank.
