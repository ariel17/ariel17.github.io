---
layout: post
title: Lessons learned
date: 2018-09-16 22:32 -0300
---

[Next: Lessons learned II]({% post_url 2018-11-03-lessons-learned-ii %})

Some lessons learned on software development in 2018:

* **Take control of the data:** when moving things around, like migrating data,
  I must think on the worst case scenario for that movement: loss? damage? Could
  you go back and restore everything? The initial movement must be a way to
  trace your steps back in time and restore the maximum amount of data modified
  (ideally everything) in the minor amount of time.

* **If something goes wrong, just rollback:** on production deployments,
  downtime is the most important metric to observe. When things doesn't go as
  expected there is no time to guess what's going on; just put back the previous
  known working version. You can found and fix the bug later, with patience and
  no hurries.

* **One thing at time:** I must take changes to production on logical steps and
  deploy them one at time, checking on every one that things go well. If I do
  multiple changes over a system and something goes wrong it could be very
  difficult (if not impossible) to determine the root cause, requiring me to
  rollback the N changes running in parallel, possibly increasing the downtime
  by the extra effort to put things back.

* **Visualize the end, know it, split it:** to effectively reach a goal, I need
  to know what success looks like: new designs, metrics, textual descriptions on
  what is required, whatever is needed to describe it. As a whole, this
  artifacts must provide me a clear destination, then I need to understand it,
  visualize in my mind the required tasks to be done and possible problems to be
  found. I should split those tasks in chunks of similar amount of effort.

  This is the most concise description of a discipline I wrote. :)

* **Check my guts:** if I see some behavior telling me that something is going
  wrong, then I should find proof that I'm wrong (unless the risk to go forth
  doesn't worth it). It is not clever to go further if I don't have information
  about the current status.
