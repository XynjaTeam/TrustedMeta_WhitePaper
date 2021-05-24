# Trusted meta design for pre-funded blockchain transactions

## Abstract
Blockchain contracts can provide a decentralized back-end that is verifiable by the public and is resistant to censorship and manipulation attempts. However while the reading/querying from the blockchain is free by definition, making changes i.e. transactions require a gas price. Which in turn enforces that everyone who wants to use such a back-end to hold some tokens and it has largely limited the actual applications of the blockchain contracts. However there has been recent developments in the blockchain technology that provides extremely low gas prices (xDai transactions cost less than $0.0001). This means that 10,000 transactions would cost about only $1. In this paper, a solution is proposed to pre-fund the blockchain transactions using the mediation of third-party willing parties that will enable the ordinary user to query and call transactions on the blockchain without requiring to hold tokens or pay a gas price.

## General architecture
A Contract is used to mediate between the willing third parties and the pre-funded contracts. We name the contract the Trusted Meta contract and the third parties Trusted Metas. The two sides register themselves on the TM contract: One to have its transactions called and the other to call the transactions. The client of the back-end contract first queries the TM contract for a weighted random list of TMs and then sends its transaction requests in the form of target contract address, the method name, method parameters, a random identifier for the call, the client’s public address, and a signature with all the previous parameters. The TM then triggers the transaction on the TM contract and sends the client’s request. The TM contract performs a health check, calls the method on the target contract passing the parameters, the client’s address and the client’s signature. The target contract validates the call using the client’s signature, performs the operations and returns. The TM contract records the response using the call identifier, then assigns the gas used plus the reward to the TM and returns. The TM returns to the client to notify that the call has ended. The client checks the result of the call using the random identifier.

![Architecture's graph](https://github.com/XynjaTeam/TrustedMeta_WhitePaper/blob/1a809be358dd4f9cee9aa8b9c20d83d0760e8951/graph%201.png)

This architecture disables the TM to make any changes to the call or to the result of the call. The target contract performs an additional health check to prevent impersonations from outside this pipeline.

## Protection against fraud and attacks
A fraud can happen by a TM generating fake calls to receive the prize. An attack to cripple the service can happen by a number of posing TMs that drop a portion of the client calls. There can also be a fraud or spam attack in which the posing TM is actually a bot that is generating fake user behavior.
The protection that the client has against posing TMs or malfunctioning TMs is to resort to the next TM provided by the TM contract or query for a fresh list of TMs.
On the TM contract, the general strategy is to reduce the chance of the suspicious TMs being selected combined with rate limiting the TMs’ calls based on its chance of being selected and the total number of calls. There will also be a rate limit for new TMs to be registered that depends on the speed of which the posing TMs can be detected and eliminated.

The last ten thousand calls are recorded per target contract for the calculations. The recording contains the time of the call and the address of the calling TM. For every new accepted call, the oldest record is removed and replaced with the new one. An integer value is assigned to every TM representing the weight (w_i) of it being selected. The weight sum (w) and the total number of TMs (n) are also known. The formulas are as follows:

```
call rate (cr) = 10,000 / (latest_record_time - oldest_record_time)
call rate of the i-th TM (cr_i) = count_of_ith_tm_calls / (latest_record_time - oldest_record_time)
weight assigned to the first TM = 1000
weight assigned to new TMs = w / n
chance of the i-th TM being selected (p_i) = w_i / w
expected call rate of the i-th TM (er_i) = p_i x 10,000 / (latest_record_time - oldest_record_time)
maximum higher call rate allowed (max_cr) = 10%
whether the call from the i-th TM is accepted = cr_i < er_i x (1 + max_cr)
```

We define conversion transactions as the transactions that are verifiable by the target contract. Meaning that if the transaction has been made by malicious intentions, it can be detected immediately or in a future time. A suspicion (sus) is an event that is triggered by the target contract and it means that a malicious behavior has been possibly detected.

For every conversion transaction we increase the selection weight of that TM by 2. If the chance of a TM being selected becomes less than 1 / (2 x n), it is flagged as a malicious TM and is removed from the pool. As the users are randomly distributed among TMs, TM weights are fairly being increased and don't affect them. But if a TM is dropping calls or is trying to make passive calls, they will eventually get removed from the TMs.

For every sus the TM loses 10% of its weight. Again, if a false sus is happening regularly, it is happening for all the TMs and causes no problems but if only a group or even a majority of the TMs are receiving sus, their chance keeps being reduced to the point that they are removed.

*Caution: The TMs can attack each other by taking actions that trigger a sus for another TM and bring them down. The motive can be increased profits for the remaining TMs or an attack to cripple the service. Hence it is advised to design the sus procedure very carefully.*
