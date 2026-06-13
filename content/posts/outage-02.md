+++
date = '2026-03-01T03:14:42+05:30'
draft = true
title = 'Outage - Sloppy DB Schema'
+++

This outage is related to the one we faced recently explained in detail in my previous blog "Outage - The Sloppy API".

## Backgroud
As I'd mentioned, our systems rely heavily over regexes for processing transactional data into separate category to profile and individual for their loan applications on behalf of our clients.

We also profile every individual's bank statements for their correctness. One such check is to validate whether the user has submitted the statement agains the corretly mentioned bank as per the lender's request. To achieve this we have worked upon a loginc over identifying the bank mismatch between the one requested and the one submitted through the bank logos present within statement (if a logo exists, there are other set of checks if no bank logo present).

This data to validate against the bank logo is again stored in out postgres DB and in the table, column schema is set to `JSONField` but pratically the data present was of type ArrayField for long integers. This was working fine since ArrayField can be stored under the JSONField parameter.

## Outage
