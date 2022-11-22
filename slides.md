---
theme: theme.json
author: DJ Adams
date: DD MMM YYYY
paging: "%d / %d"
---

# Talk

Building event mesh driven architectures

# Speaker

DJ Adams, Developer Advocate at SAP

# Agenda & content

* General concepts
* Specific ideas in SAP Event Mesh 
* Tangible artifacts
* Simple CAP-based sender and receiver samples
* Further reading & resources
* Questions & answers

This talk's content is available online in a GitHub repo: [qmacro/event-mesh-talk][repo]

[repo]: https://github.com/qmacro/event-mesh-talk/

---

# General concepts

Connecting systems via events is different.

## Traditional connectivity

* Often implemented with HTTP (see note)
* Point-to-point, synchronous calls
* Tightly coupled

## Event based connectivity

* Implemented with HTTP but also with other protocols
* Brokered, asynchronous
* Loosely coupled

_HTTP can be used asynchronously - see status [202 ACCEPTED](https://httpwg.org/specs/rfc9110.html#status.202)_

---

# Level 1

## Level 2

### Level 3

#### Level 4

