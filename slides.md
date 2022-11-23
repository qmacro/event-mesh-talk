---
theme: theme.json
author: DJ Adams
date: DD MMM YYYY
paging: "%d / %d"
---

# Talk

Building event mesh driven architectures with SAP Event Mesh

# Speaker

DJ Adams, Developer Advocate at SAP

# Agenda

* General concepts
* Specific details about SAP Event Mesh 
* Live demo exploring core building blocks
* Further reading & resources
* Discussion

This talk's content is available online in a GitHub repo: [qmacro/event-mesh-talk][repo]

[repo]: https://github.com/qmacro/event-mesh-talk/

---

# General concepts

Connecting systems via events is different.

## Traditional connectivity

* Often implemented with HTTP
* Point-to-point, synchronous calls (see note)
* Tightly coupled
* Payload oriented

## Event based connectivity

* Implemented with HTTP but also with other protocols
* Brokered, asynchronous
* Loosely coupled
* Message oriented
* See also [publish/subscribe][pubsub]

_HTTP can be used asynchronously - see status [202 ACCEPTED](https://httpwg.org/specs/rfc9110.html#status.202)_

[pubsub]: https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern

---

# Specific details about SAP Event Mesh 

* Service on SAP Business Technology Platform
* Central broker for SaaS, on-prem and custom apps & services
* Various plans available
* Different protocols supported
* Management, messaging and events APIs
* Building block concepts

```bash
curl \
  --silent \
  --url 'https://raw.githubusercontent.com/SAP-samples/btp-service-metadata/main/v0/developer/enterprise-messaging.json' \
  | jq .description
```

> Resource from [BTP Service Metadata][btp-service-metadata]

[btp-service-metadata]: https://github.com/SAP-samples/btp-service-metadata

---

# Service on SAP BTP, various plans available

The Event Mesh service (previously called Enterprise Messaging) provides broker facilities.

## Standard SAP BTP accounts

* Default plan
* Connectivity plan (beta)

## Trial SAP BTP accounts

* Dev plan (deprecated)

_We'll use the `dev` plan in trial for the `enterprise-messaging` service_

---

# Different protocols supported

* HTTP (1991) - [HyperText Transfer Protocol][http] 
* MQTT (1999) - [Message Queuing Telemetry Transport][mqtt] 
* AMQP (2003) - [Advanced Message Queuing Protocol][amqp] 

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

# Building block concepts

All scripts available in [SAP-samples/cloud-messaging-handsonsapdev][cloud-messaging-handsonsapdev]:

* `service-setup`
* `cleanup`
* `setup`
* `management`
* `messaging`
* `getapproute`

Will also need Cloud Foundry CLI:

* `cf`

Demo is designed to be run in a trial SAP BTP subaccount. 

[cloud-messaging-handsonsapdev]: https://github.com/sap-samples/cloud-messaging-handsonsapdev

---

# Building block concepts

```bash
# Clone the repository and move into the directory
git clone https://github.com/sap-samples/cloud-messaging-handsonsapdev
cd cloud-messaging-handsonsapdev

# Set up environment variables, source completion & add current dir to PATH
vi settings.sh
source setup

# Log in to CF and push the simple HTTP receiver
cf login -a <CF API endpoint>
cd webhook/ && cf push $webhookapp && cd ..

# Create service instance
service-setup
```

At this point we're ready to explore the building blocks with the API endpoint wrappers.

---

# Create queue

```text







                         ┌─────────┐
                         │  QUEUE  │
                         └─────────┘




```

```bash
management create_update_queue q1
```

---

# Publish message to queue

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
  messaging publish_message_to_queue q1 $x
done
```

---

# Consume message directly from queue


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
management get_queue q1 # check details of queue content
messaging consume_message_from_queue q1
```

---

# Consume messages via webhook

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
cf a # list apps to show the simple HTTP receiver 
management get_queue q1 # check details of queue content again
messaging create_webhook_subscription whs1 q1 $(getapproute $webhookapp)
cf logs --recent $webhookapp | grep got
```

---

# Publish message to topic

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
management create_update_queue_subscription q1 topic1
messaging pause_webhook_subscription whs1 # temporarily stop draining to see msg in queue
messaging publish_message_to_topic topic1 eins
management get_queue q1 # see that this message is now in the queue
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

* Topic patterns
* Webhook subscriptions
  * Status 
  * Handshakes
  * Authentication
* Quality of service
* Support for CloudEvents

---

# Extra material

## Documentation and specifications

* [SAP Help Portal documentation][help-portal-docu]
* [Cloud Events v1.0 specification][cloud-events-spec]
* [CAP Cookbook: Events and Messaging][cap-cookbook]

## Repositories

* [SAP-samples/cloud-messaging-handsonsapdev][cloud-messaging-handsonsapdev]
* [qmacro/event-mesh-talk][event-mesh-talk]

## Video series

* [Diving into messaging on SAP Business Technology Platform][messaging-playlist]

[help-portal-docu]: https://help.sap.com/docs/SAP_EM/bf82e6b26456494cbdd197057c09979f/df532e8735eb4322b00bfc7e42f84e8d.html
[cloud-events-spec]: https://github.com/cloudevents/spec/blob/v1.0/spec.md
[messaging-playlist]: https://www.youtube.com/playlist?list=PL6RpkC85SLQCf--P9o7DtfjEcucimapUf
[cap-cookbook]: https://cap.cloud.sap/docs/guides/messaging/
[cloud-messaging-handsonsapdev]: https://github.com/sap-samples/cloud-messaging-handsonsapdev
[event-mesh-talk]: https://github.com/qmacro/event-mesh-talk

