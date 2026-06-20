# MoneyWhere
App that keeps track of your subscriptions

# Architecture (For now)
![Architecture Image](<Architecture Designing\archi.png>)


# Services
** Keep in mind this is a MVP so it may not convolve to production standard frameworks :)
## Atomic
### Parser
Parses raw statements to get rows of raw transactions and standardizes transaction layout

### Normalizer
Removes suffixes (e.g. " SG" or "SG"), normalizes vendors/merchant strings to a clean merchant token ensuring standardization (e.g. "NTFLIX"/"NETFLIX SG" to "netflix")

### Deduper
Checks whether a transaction has already been processed to prevent double entries

### Referencer
Queries normalized merchant token to find existing subscriptions already logged previously

### Inferer
For when Referencer fails, runs a model against the transaction to figure out whehter its a subscription along with the category

### Intervaler
Looks at the transaction history based on a user's statements and identifies the frequency. Adds new records into same merchant table for faster future use


## Composite
### InjestStatement
Orchestrates Parser, Normalizer and Deduper

### RecognizeSubscription
Orchestrates Referencer, Inferer and Intervaler

### SyncDashboard
In charge of pushing live updates via Websocket


# Others
## API Gateway
Acts as an in-between with auth preventing users from directly interacting with services

## Message Queues
- One queue in between InjestStatement and RecognizeSubscription
- One queue in between RecognizeSubscription and SyncDashboard

## Websocket
Placed between SyncDashboard and Frontend for live updates

## Caching
- One for the Deduper for fast merchant refers
- One for Dashboard details for faster frontend loading

## Bucket
For storing of statement files


# Framework
| Component | Framework | Language |
| -------- | ------- | ------- |
| Atomic Services| Gin / FastAPI | Go / Python
| Composite Services | FastAPI | Python
| API Gateway | Kong | idk LOL
| Message Queue | ElasticMQ | idk LOL
| Websocket | Fastify | JS
