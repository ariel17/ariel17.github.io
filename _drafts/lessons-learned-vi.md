---
layout: post
title: Lessons learned VI
---

* **I have the control of the API and its data**: Modeling data and integrity
  validation is my responsability, despite the fact that clients will make
  checks on their side.

* **Migrating an API endpoint to another one must satisfy behaviour**: I have
  to check same body fields and verify if other paths are being affected with
  the change, like "migrating /old/path/[0-9]+ -> /new/path/[0-9]+... what
  happens with /old/path/[0-9]+/depper?"
