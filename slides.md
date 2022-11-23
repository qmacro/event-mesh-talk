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

_We'll use the `dev` plan in trial for the `enterprise-messaging` service_

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

## Management

* Queues
* Queue subscriptions

## Messaging

* Message publication (to queues and topics)
* Message consumption
* Webhook subscriptions
* Webhook subscription handshakes
* Consumption acknowledgments

## Events

* For CloudEvents

---

# Concepts that form building blocks

All scripts available in [SAP-samples/cloud-messaging-handsonsapdev][cloud-messaging-handsonsapdev]:

* `cleanup`
* `setup`
* `management`
* `messaging`
* `getapproute`

Clone the repository, and source the `setup` file to start:

```bash
. setup
```

[cloud-messaging-handsonsapdev]: https://github.com/sap-samples/cloud-messaging-handsonsapdev

---

# Queue

```text







                         ┌─────────┐
                         │  QUEUE  │
                         └─────────┘




```

```bash
management create_update_queue Q1
```

---

# Publishing message to queue

```text

                           publish

                              │
                              │
                              │
                              ▼
                         ┌─────────┐
                         │  QUEUE  │
                         └─────────┘




```

```bash
for x in one two three; do 
  messaging publish_message_to_queue Q1 $x
done
```

---

# Consuming message directly from queue


```text

                           publish

                              │
                              │
                              │
                              ▼
                         ┌─────────┐
                         │  QUEUE  │
                         └────┬────┘
                              │
                              │
                              └──────────────────►  consume

```

```bash
management get_queue Q1 # check details of queue content
messaging consume_message_from_queue Q1
```

---

# Consuming messages via webhook

```text

                           publish

                              │
                              │
                              │
                              ▼
                         ┌─────────┐   webhook    ┌─────────┐
                         │  QUEUE  ├──────────────┤ WEBHOOK │
                         └────┬────┘ subscription └─────────┘
                              │
                              │
                              └──────────────────►  consume

```

```bash
cf a # list apps; qmacro-webhook is a simple Node.js endpoint (in `webhook/`)
management get_queue Q1 # check details of queue content again
messaging create_webhook_subscription WHS1 Q1 $(getapproute qmacro-webhook)
cf logs --recent qmacro-webhook | grep got
```

---

# Publishing message to topic

```text

  publish

     │
     │
     │
     ▼
┌─────────┐    queue     ┌─────────┐   webhook    ┌─────────┐
│  TOPIC  ├──────────────┤  QUEUE  ├──────────────┤ WEBHOOK │
└─────────┘ subscription └────┬────┘ subscription └─────────┘
                              │
                              │
                              └──────────────────►  consume

```

```bash
management create_update_queue_subscription Q1 TOPIC1
messaging pause_webhook_subscription WHS1 # temporarily stop draining to see msg in queue
messaging publish_message_to_topic TOPIC1 eins
```
---

# One queue for multiple topics

```text



┌─────────┐
│  TOPIC  ├───────┐
└─────────┘       │
                  │
┌─────────┐       │      ┌─────────┐
│  TOPIC  ├───────┼──────┤  QUEUE  │
└─────────┘       │      └─────────┘
                  │
┌─────────┐       │
│  TOPIC  ├───────┘
└─────────┘
```

---

# One topic subscribed to by multiple queues

```text



                         ┌─────────┐
                 ┌───────┤  QUEUE  │
                 │       └─────────┘
                 │
┌─────────┐      │       ┌─────────┐
│  TOPIC  ├──────┼───────┤  QUEUE  │
└─────────┘      │       └─────────┘
                 │
                 │       ┌─────────┐
                 └───────┤  QUEUE  │
                         └─────────┘
```

---

# Further details

* Webhook subscription status and handshakes
* Quality of service
* Topic patterns
* Support for CloudEvents

---

# Extra material

## Documentation and specifications

* [SAP Help Portal documentation][help-portal-docu]
* [Cloud Events v1.0 specification][cloud-events-spec]
* [CAP Cookbook: Events and Messaging][cap-cookbook]

## Repositories

* [SAP-samples/cloud-messaging-handsonsapdev repository][cloud-messaging-handsonsapdev]
* [qmacro/event-mesh-talk][event-mesh-talk]

## Video series

* [Diving into messaging on SAP Business Technology Platform][messaging-playlist]

[help-portal-docu]: https://help.sap.com/docs/SAP_EM/bf82e6b26456494cbdd197057c09979f/df532e8735eb4322b00bfc7e42f84e8d.html
[cloud-events-spec]: https://github.com/cloudevents/spec/blob/v1.0/spec.md
[messaging-playlist]: https://www.youtube.com/playlist?list=PL6RpkC85SLQCf--P9o7DtfjEcucimapUf
[cap-cookbook]: https://cap.cloud.sap/docs/guides/messaging/
[cloud-messaging-handsonsapdev]: https://github.com/sap-samples/cloud-messaging-handsonsapdev
[event-mesh-talk]: https://github.com/qmacro/event-mesh-talk

