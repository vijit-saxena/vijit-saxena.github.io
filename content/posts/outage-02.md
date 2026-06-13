+++
date = '2026-03-01T03:14:42+05:30'
draft = false
title = 'Outage - Sloppy DB Schema'
+++

This outage is related to the one we faced recently explained in detail in my previous blog "Outage - The Sloppy API".

## Background
As I'd mentioned, our systems rely heavily over regexes for processing transactional data into separate categories to profile an individual for their loan applications on behalf of our clients.

We also profile every individual's bank statements for their correctness. One such check is to validate whether the user has submitted the statement against the correctly mentioned bank as per the lender's request. To achieve this we have worked upon a logic over identifying the bank mismatch between the one requested and the one submitted through the bank logos present within the statement (if a logo exists, there are other set of checks if no bank logo is present).

This data to validate against the bank logo is again stored in our postgres DB and in the table, column schema is set to `JSONField` but practically the data present was of type `ArrayField` for long integers. This was working fine since `ArrayField` can be stored under the `JSONField` parameter.

## Outage
This outage was a mix of:
1. <!-- root cause 1 -->
2. <!-- root cause 2 -->

### <!-- Root Cause 1 Heading -->
<!-- Detail what happened -->

### <!-- Root Cause 2 Heading -->
<!-- Detail what happened -->

## Resolution
1. <!-- Step 1: short title -->
<!-- Explanation -->

2. <!-- Step 2: short title -->
<!-- Explanation -->

## Learnings
1. <!-- Learning 1 -->
2. <!-- Learning 2 -->

## Actions
1. <!-- Action 1 -->
2. <!-- Action 2 -->
