---
layout: post
title: "MQTT topic wildcards"
date: 2023-07-04 07:00:00 -0000
categories: protocols
---
In the realm of Internet of Things (IoT) and machine-to-machine (M2M) communication, MQTT (Message Queuing Telemetry Transport) has emerged as a lightweight and efficient protocol. MQTT's publish-subscribe model enables devices to exchange messages in a scalable and decoupled manner. One of the key features that make MQTT highly versatile is the ability to use wildcards in topic subscriptions. Wildcards allow for flexible and dynamic filtering of messages, opening up a world of possibilities for building intelligent and adaptable IoT systems.

MQTT topics serve as the channels through which devices communicate with each other. A topic is a hierarchical structure represented as a string, with segments separated by forward slashes (`/`). For example, a topic could be "`sensors/temperature/room1`" to represent the temperature sensor in "room1". MQTT supports three types of wildcards in topic subscriptions: the plus sign (`+`), the hash sign (`#`), and the literal segment.

### The Plus Wildcard (+)
The plus wildcard allows for matching a single segment within a topic. It can substitute any character at a particular level of the hierarchy. For example, subscribing to "`sensors/+/room1`" would match "`sensors/temperature/room1`" and "`sensors/humidity/room1`", but not "`sensors/temperature/livingroom`". The plus wildcard provides a simple yet effective way to capture messages from multiple devices or sensors within a specific context.

### The Hash Wildcard (#)
The hash wildcard is more powerful and flexible, as it can match multiple segments within a topic. It can be used only at the end of a topic string, and it represents any number of levels in the hierarchy. For example, subscribing to "`sensors/temperature/#`" would match "`sensors/temperature/room1`", "`sensors/temperature/room2`", and even "`sensors/temperature/room1/bed`". The hash wildcard is useful when you need to capture messages from various sensors or devices across different locations or subtopics.

### The Literal Segment:
While not technically a wildcard, the literal segment allows for exact matching of a specific segment within a topic. This is achieved by explicitly specifying the desired value in the topic string. For instance, subscribing to "`sensors/temperature/room1`" would only match messages from the temperature sensor in "`room1`". Literal segments are handy when you want to filter messages based on a specific device or location.

By combining these wildcard characters and literal segments, MQTT subscribers can tailor their topic subscriptions to precisely capture the messages they need. Wildcards offer tremendous flexibility and scalability, making MQTT an ideal choice for IoT deployments with evolving device networks and dynamic environments.

## Here are a few scenarios that demonstrate the power of wildcards in MQTT topics

Sensor Aggregation:
Consider a scenario where you have multiple temperature sensors in different rooms. By subscribing to the topic "`sensors/temperature/+`", you can efficiently aggregate temperature data from all the sensors without explicitly subscribing to each sensor's topic individually.

Multi-level Monitoring:
In a smart building with multiple floors and rooms, you can use the topic "`sensors/+/+/temperature`" to capture temperature data from all rooms on all floors. This wildcard subscription allows you to monitor and analyze temperature trends across the entire building.

Device Management:
When managing a large fleet of devices, using wildcards can simplify administration tasks. For instance, you can subscribe to the topic "`devices/+/status`" to receive status updates from all devices, regardless of their specific type or location.

While wildcards provide great flexibility, it's important to use them judiciously. Overusing wildcards can lead to a high volume of irrelevant messages being received, which may impact system performance and increase network bandwidth usage.

In conclusion, wildcards in MQTT topics empower IoT and M2M systems with adaptable and efficient messaging. They enable subscribers to capture messages dynamically, simplifying data aggregation, multi-level monitoring, and device management. By leveraging the power of wildcards, developers can design intelligent and scalable IoT solutions that effectively handle diverse device networks and changing environments.