---
id: transfers-recomputation
title: Transfers Recomputation
---

The Rucio transfer pipeline supports dynamic recomputation functionality
in the request pipeline. Due to the linear nature of the conveyor system
in Rucio, there is a significant lag time between the initial submission of
a request and when it is sent to the transfer tool of choice (often, FTS3).

Therefore, to enable the utilization of the most updated information,
Rucio operators may configure recomputation functionality that will allow for
Rucio requests to receive updated information in-flight.

There are two major components of this feature.
- The throttler has a check to reset stale waiting requests on timeouts. The default
  uses a 1-day limit on requests before they are eligible for resetting. This figure
  is based on historical data as requests can often stay in the throttler for days on end.
  Requests that are reset will be sent back to the Preparer daemon and recompute their sources.
- There is a message queue between the finisher and the throttler. A message is fired off
  whenever the finisher determines that a transfer has been completed. It will then send
  a message to the throttler to inform it to reset all throttled transfers that rely
  on the replica that is now available, resetting them to the preparing state to allow for
  recomputation of a source.
