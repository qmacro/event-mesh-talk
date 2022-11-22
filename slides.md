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
* See also: publish/subscribe, messaging

_HTTP can be used asynchronously - see status [202 ACCEPTED](https://httpwg.org/specs/rfc9110.html#status.202)_

---

# Specific ideas in SAP Event Mesh 

* Service on SAP BTP, various plans available
* Different protocols supported
* Management, messaging and events APIs
* Concepts that form building blocks

---

# Service on SAP BTP, various plans available

The Event Mesh service was previously called Enterprise Messaging.

## Standard SAP BTP accounts

* Default plan
* Connectivity plan (beta)

## Trial SAP BTP accounts

* Dev plan (deprecated)

_We'll use the `dev` plan for the `enterprise-messaging` service_

---

# Different protocols supported

* HTTP - [HyperText Transfer Protocol][http]
* AMQP - [Advanced Message Queuing Protocol][amqp]
* MQTT - [Message Queuing Telemetry Transport][mqtt]

[http]: https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol
[amqp]: https://en.wikipedia.org/wiki/Advanced_Message_Queuing_Protocol
[mqtt]: https://en.wikipedia.org/wiki/MQTT

---

# Management, messaging and events APIs

Different APIs for different purposes.

## Management API

## Messaging API

## Events API

