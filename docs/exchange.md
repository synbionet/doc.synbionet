# Exchange Contract
Defines the fair exchange protocol for services.

# Overview
Each service will have many exchanges.  In the current version, each exchange is a unique contract between parties.  The exchange acts as an escrow service and manages the release of funds based on the state machine below.

The are 3 primary actors in an exchange:
- Buyer
- Seller
- Moderator (optional)

# State Machine
```mermaid
stateDiagram
    [*] --> Offer: seller
    Offer --> Fund: buyer
    Offer --> Void: *offer_timer* 15 days

    Fund --> Complete: buyer
    Fund --> Dispute: buyer (if moderator)
    Fund --> Complete: *dispute_timer* configurable

    Dispute --> Resolve: moderator
    Resolve --> Complete: buyer or seller
    Dispute --> Complete: *resolve_timer* 30 days
    Complete --> [*]: funds released
    Void --> [*]
``` 

## Buyer selects a service
1. Buyer searches the Bionet dappUI for a service, i.e. *create a protein*.
2. When the buyer finds a service they're interested in, the can contact the service provider.  The current state of synthetic biology often requires *talking to someone* in the initial phases of the service.  One of the decisions will be whether the exchange will require a *moderator*. And if so, who the moderator is. More on the moderator below. 

## Seller creates an offer
1. Once an agreement is reached, the seller will create an Exchange (Offer) that's unique to the buyer/seller, terms, moderator, etc...
2. The creation starts a 'timer'. The offer is only good for 15 days. If the buyer doesn't commit to the offer by funding it within 15 days, the offer is voided.
## Buyer funds the exchange
1. If the offer hasn't expired, the buyer can commit to it by sending a signed transaction that includes the funds equal to the agreed upon price of the service.
2. The funds are escrowed by the Exchange. Ensuring the seller will get paid if everything goes as expected.
3. A configurable timer is started to ensure the protocol continue to move forward.

During this time is when the seller is performing the service, shipping, etc...

Once the buyer receives the product, they have 3 options:
1. Sign off of the exchange. Moving to the *complete* state.
2. Do nothing. Let the timer expire which will a automatically move to the *complete* state. Or...
3. Dispute the exchange

## Exchange Complete
Once the exchange reaches the *complete* state, the escrowed funds are transfered to the seller, and a protocol fee is paid to the bionet (unless there is a dispute). The seller is responsible for protocol fees:
```text
DueProtocol = price * protocol fee
DueSeller = service price - DueProtocol
```

An exchange is officially locked and closed in the *complete* state

## Disputes
Dealing with disputes between buyer and seller can be difficult. Based on ideas from [OpenBazaar](https://en.wikipedia.org/wiki/OpenBazaar) the Bionet uses the concept of a *moderator* to help resolve disputes.

**What is a moderator?**
A moderator is a vetted member of the Bionet that can serve as an intermediary in the event of a dispute in an exchange.  During the creation of an Offer, the buyer and seller can optionally decide on a moderator to resolve any potential issues with the exchange of the service. Resolution by a moderator will result in a refund. There are 3 types of possible refunds:
- **Full:** Buyer will receive a full refund 
- **Partial:** Buyer will receive a 50% of the service price
- **None:** No refund given. The original terms of the exchange apply 

**Rules on the use of moderator**
- An exchange may or may not have a moderator
- If the buyer and seller trust each other in the exchange, they can elect not to have a moderator. This is referred to as a **direct** exchange.  A direct exchange has no *dispute* phase in the protocol.  
- A service offered for free does not have an moderator and dispute phase.
- A moderator receives a fee for their service paid by the buyer. Unless the dispute is ruled "no refund" in which case, the seller pays the moderator fee.
- A moderator is a service on the bionet. The moderator will advertise their fee and rules on how they handle a dispute.
- If a dispute is filed by a buyer, the moderator receives their fee regardless of the type of refund.
- A bionet protocol fee is NOT collected in the event of a dispute.
- Resolving a dispute requires a threshold of 2-of-3 signatures from the parties to the exchange (moderator, buyer, seller).

Calculating final balances:

**Full Refund**
```
DueModerator = price * fee
DueBuyer =  price - DueModerator
```
**Partial Refund**
```
half = price / 2
DueModerator = price * fee
DueBuyer = half - DueModerator
DueSeller = half
```
**No refund**
```
DueBuyer = 0
DueModerator = price * fee
DueSeller = price - DueModerator
```
An invariant of the exchange: Escrowed funds should always equal the sum of all payouts.
```
Escrowed Funds == dueSeller + dueBuyer + dueProtocol + dueModerator
```


## Buyer makes a dispute
1. If the fund phase timer has not expired, the buyer can activate a dispute.
2. A dispute timer is started and is active for 30 days.
3. If the dispute is not resolved within 30 days, the exchange completes as normal and the moderator does not receive their fee.

During the dispute phase, the moderator collects the needed information from both the buyer and seller to make a decision on the appropriate refund (if any).  Once the moderator makes a decision on the type of refund (1-of-3 required signatures), It's up to either the buyer or seller to sign off on the agreement to fulfill the 2-of-3 signature requirement.  Signatures are accomplished by the party sending a signed transaction the contracte *resolve* function.

Once the dispute is resolved and signed off on, the funds are released to their respective parties and the exchange moves to the *complete* state.

## Events
During the flow of the exchang, several different events are emitted from the contract that describe what's happening.  Those events are collected by the *dApp Indexer* and made available to the *dApp UI*.











