+++
date = '2026-02-27T02:03:12+05:30'
draft = false
title = 'Outage - The Sloppy API'
+++

## Background
So we work with trasactional data, extracting it either via PDF bank statements or through AccountAggregators, and categorising each transaction into various categories like Investment, Lender Txn, Self Transfers, identifying merchant transfers, etc and building insights over the data to help lender evaluate the loan applicant's financial profile. For this categorisation part, we rely heavily on regex at the moment, identifying patterns for each transaction category.

There is a constant need for continious updates for regex patterns which we store in our centralised DB cluster, postgres.

Until some time an Admin user used to manually update the regexes directly into the DB via queries, we could hold up to this approach for the initial phase but when the volume started to grow, we felt a need for an API which would do the same for us. So we created one, about which we later realised was only a half baked idea.

We created an API which would take the regex pattern and append it to an appropriate column of a table, basically all the API did was to run the query which we ran manually. Initially there were neither type validations in place nor a double check for the regex correctness.

## Outage
This outage was a mix of two things
1. Human Error/Negligency
2. Bad error handling
3. The Sloppy API

The API worked very well but only until a few days down the line when one malformed regex was entered into the database.

### Human Error/Negligency
This was on the first hand pure negligence of adding the API directly without proper testing. Negligence was to the level that our regex library couldn't even compile it.

Once the regex was live, upon receiving live traffic for some time nothing happened, since all cached regex data from redis cluter was being pulled but after the TTL crossed, everything started to fail. Our transaction categorisation started to fail and pods started to trigger sentries. Now here, this was purely a negligence to have inserted such a regex into our system.

Our conversion rates dropped to near zero and panick kicked in.

Although the regex was identified in no time and was removed from DB as well as redis the impact was huge, to the scale where our queues got bloated with close to 20 million transactions.

### Bad Error Handling
Also our code was written in such a way that there was absolutely no error handling for regex compilation, it was an assumption we had made about the regex correctness for compilation, resulting in the killing of process instead of handling the exception and moving on to next regex, our process within the pod was killed abruptly.

Now these pods are a consumer sitting behind a SQS where each queue event is a batch of transaction data to be worked upon. Since the process was killed abruptly, the message was not deleted from the SQS and visibility timeout was set to 30 mins, meaning the messages would automatically reappear for processing in the SQS after the set visibility timeout. 

This peaked our latencies resulting in delay of service for 20 million records by 30 minutes at least. Which is a very high cost for the product to pay both in terms of cost as well as the credebility in the market.

### The Sloppy API
When the API was created. Apart from AuthN/AuthZ there were basic level of API validations in place but regex correctness was an assumed validation, basically believing the user for the correctness of it. Making this assumption cost us the outage.

## Resolution
For the first part, when the queue got so much bloated that even our EKS quota limits reached to it's maximum we took the below 2 decisions as the first line of defence.
1. Redirect traffic to new queue and eject existing one
We decided to eject the current bloated queue from the system and replace it with a fresh queue to receive live traffic. This made sure any new traffic coming to our system does not get delayed, since we had a FIFO queue and all impacted traffic can be served separately. This resolved the high latency issue for all clients we were serving.

2. Create a temporary consumer for bloated queue
We then created a temporary consumer for the bloated queue to process the 20 million stuck events gradually to completion. This consumer took it's time but eventually processed all messages.

3. Revert back to original state
Once the outage was fully taken care of, the bloated queue as well as temporary consumer pods were terminated.

## Learnings
1. Never to trust an API user, (however tech literate he/she could be), always have a very strong validation layer before making any DB writes.
2. Always handle vulnerable part of the code for an error, to proceed gracefully where ever possible.
3. In case of outages, first level of defence should be to bring the application to a stable state instead of debugging during the outage. Had we migrated traffic to new queue early, number of client operations getting stuck could have been way lesser than it was.
4. Also at this point we lack a proper production rollout plan eg. blue-green or canary based techniques.

## Actions
1. Team has starte to add a strong validation layer over the API, listing out all possible issues a DB write could cause.
2. Also all regex compilation is now being handled gracefully to proceed with only valid regexes.
3. There are discussions ongoing regarding adding a feature flag for each regex DB write to test it internally first by QA before making it live on production.
4. Also discussions have started to identify and implement a suitable production rollout strategy.