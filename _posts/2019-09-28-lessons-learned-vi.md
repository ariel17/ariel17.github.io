---
layout: post
title: Lessons learned VI
date: 2019-09-28 00:13 -0300
---

## On Life itself

* **Life is not about one problem at time**: Many problems will happen at the
  same time and there will be moments where almost *all* of them have to be
  attended. Work chanllenges, house maintenance, family health (and personal
  one!) happens in parallel without asking if its the correct moment to broke.

* **Work behavior and other life aspects reflects are related, they are not
  isolated environments**: If something is not going well in the office (i.e.
  the way you organize your daily rutine) it is very likely that the same can
  be observed in other aspects of your like, like the way you organize your
  work in home during weekend or working days.

  This maybe does not reveal nothing life-changing, but it can help to discover
  relationships between mental behaviour in different situations.

## On management

### All members have to take part on the success of the team

* **The scenario:** A brand new project has been putted on the table; a due
  date was fixed in a near future.

* **The decision:** All senior developers were hands-on over the hard tasks; a
  junior developer on the team was left with non-urgent tasks. Coaching were
  left unattended.

* **The consecuence:** Junior mates were unmotivated, not feeling part of the
  whole.

## On API development

* **I have the control of the API and its data**: Modeling data and integrity
  validation is my responsability, despite the fact that clients will make
  checks on their side.

* **Migrating an API endpoint to another one must satisfy behaviour**: I have
  to check same body fields and verify if other paths are being affected with
  the change, like "migrating `/old/path/[0-9]+` -> `/new/path/[0-9]+...` what
  happens with `/old/path/[0-9]+/deeper`?"
