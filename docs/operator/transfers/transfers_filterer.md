---
id: transfers-filterer
title: Transfers Filterer
---

`conveyor-filterer` (transfer filterer) is a preliminary filterer for the throttler.

The intuition is that in an ideal world, Rucio would continue recomputing request routing
and source selection up until the very moment that the specific request is submitted to the
chosen transfer tool (FTS3, Globus Online, etc.). This isn't feasible, and Rucio instead
relies on greedy source selection either in the optional `conveyor-preparer` daemon or the 
`conveyor-submitter` daemon. We would like for requests of the same DID to have access to
replicas that we know will be available in the near future.

Thus, this daemon functions
as a way to batch and partition requests of the same DID so only a certain percentage
are processed at any given time. This enables the later partitions to have access to more
available replicas and have a larger selection of possible sources and paths.

You can only enable this daemon if both the preparer and throttler daemons are activated.
Turn on the `conveyor-filterer` daemon by setting the `conveyor/use_filterer = True`
configuration option.

Filterer:
- Gets all the requests that have passed through the `conveyor-preparer` and have
  entered the `Batch_Filtering` state
- Groups the requests based on unique DIDs
- Detects if there are already requests of that specific DID in the conveyor
  up to a certain threshold
- Transitions filtered requests back to the `Preparing` state, allows remaining requests
  to move on to the throttler
