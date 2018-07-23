# SCTE-104/35 Signalling

## Generic design

DRAFT  
Author: Keith Millar  
Date: 01/05/2018   
Version: 0.1  


- Introduction
- High Level Requirements
   - Generic Requirements
   - Carriage within (H)SDI
   - SCTE-104 over TCP/IP connection to inserter (encoder)
   - Inserter requirements
- Marker Solution Overview
   - SCTE-104
   - Multiple message flows
      - Splice Insert Messages
      - Multiple Segmentation Descriptors
   - SCTE-104 over VANC
      - Multiple Segmentation Descriptors
      - Single message per frame
   - SCTE-104 over TCP/IP
- Generic capabilities
   - PID mapping
   - Message Aggregation
   - SCTE-104 processing configurations
- SCTE-104/35 Message Types
   - Splice_Insert
   - Time_Signal
   - Splice_Null Messages


## 1. Introduction
This document outlines the requirements and solution for the signaling of markers
and other commands within both the Broadcast contribution feed and the
downstream broadcast feed.

Within a distribution system a single broadcast contribution feed can be used to
support multiple broadcast feeds. For example a single feed could support a
simulcast IPTV service, DTT Service and a DTH service, where each broadcast
service requires a different set of markers.

The document describes how this can be supported in a flexible way.
The primary focus of DVB is the signalling of Break and Spot markers, however a
more holistic view needs to be considered as there is a desire by broadcaster to
also signaling boundaries such as Programmes and Chapters, along with other
creative uses.
    
Within the broadcast industry SCTE-104 is today widely used for the signalling of
markers to the inserters (encoders) and SCTE-35 for signalling of markers to end
devices. ITV sees no reason for this to change and therefore the document
concentrates on how these existing standards can be used to meet the needs of the
broadcasters.

## 2. High Level Requirements
The following are considered the main high level requirements for signalling
boundary markers.


### 2.1. Generic Requirements
- The ability to insert/update a marker independently of another marker. I.e.
should not require the complete set of markers for a given time point to be
sent again as a single message. This can also be seen as supporting
separate workflows for the different marker requirements.
- Support for existing devices that make use of SCTE-35 Splice Insert
messages.
- Support for existing inserters (encoders) that make use of SCTE-104 Splice
Insert messages.
- The ability for the inserter (encoder) via configuration to ignore markers
within the contribution feed. This is required as within some downstream
delivery chains they are not required and may be undesirable.
### 2.2. Carriage within (H)SDI
- The ability to signal Break markers within the contribution feed.
- The ability to signal Spot markers within the contribution feed.
- The ability to signal Programme markers within the contribution feed.
- The ability to signal Chapter markers within the contribution feed.
2.3. SCTE-104 over TCP/IP connection to inserter (encoder)
● The ability to signal Break markers to the inserter (over TCP/IP).
● The ability to signal Spot markers to the inserter.(over TCP/IP)
● The ability to signal Programme markers to the inserter (over TCP/IP).
● The ability to signal Chapter markers within the contribution feed (over
TCP/IP).
2.4. Inserter requirements
● Carriage of markers of different types (e.g. Splice Insert, Programme,
Chapter, etc), on separate MPEG-PIDs within a service. Using
PDI_PID_index of SCTE-104 to map to MPEG-PIDs.
● Support for signalling if inserter (encoder) should perform stream
conditioning for a given SCTE-104 messages.
● Support for signalling if inserter (encoder) should process an SCTE
message.

3. Marker Solution Overview

As can be seen from the diagram above there can be an number of markers with
the same time point. However this should not force them all to be signalled as a
single message (SCTE-104 or SCTE-35).
SCTE-104 is the industry standard mechanism for signalling markers to an inserter
(encoder), and these SCTE-104 messages can be delivered to the inserter using
SDI-VANC or TCP/IP. The following sections describe the use of these 2 delivery
mechanisms.
3.1. SCTE-
3.1.1. Multiple message flows
To support the requirement for inserting/updating a marker independently of another
marker, it is required to have separate message flows for each Use Case. Within
SCTE-104 this is supported by the DPI_PDI_Index field, where each flow has a
unique DPI_PDI_index.
3.1.2. Splice Insert Messages
Today a Splice Insert message is by far the most common method used for
signalling Spot and Break positions within a A/V stream. Typically a Splice Insert for
a “from_network” (i.e start of a Spot or Break) is sent and the inserter/device uses
the duration declared within the Splice Insert message to determine the
“to_network” point.
It is imperative that Splice Insert messages are supported within a DVB-TA
standard, as they are widely supported within inserters (encoders) and end devices.

3.1.3. Multiple Segmentation Descriptors
The SCTE-104/35 specifications support the use of Segmentation Descriptors which
provide an alternative way of signaling Spot and Break positions, and also allow the
signalling of non advertising markers such as Programme and Chapter boundaries.
It is possible to include multiple segmentation descriptors within a single SCTE
104/35 message however this has a number of potential operational/deployment
issues as follows:
● The playout events that signal the insertion of an SCTE-104 message may
come from multiple sources and therefore its potentially problematic to
combine them (if they have the same time point) to generate a single
SCTE104 message.
● End devices are likely to be more resilient if the SCTE-35 resulting from the
SCTE-104 fits within a single MPEG-TS packet (or a small number of
packets).
Therefore to ease operational and deployment issues it is recommended that a
SCTE-104 message shall contain one segmentation descriptor. Segmentation
descriptors may be included within a Splice_insert, Splice_null and a time_signal
message, and it is recommended that DVB constrains the use of the segmentation
descriptor to the time_signal message.
3.2. SCTE-104 over VANC
3.2.1. Multiple Segmentation Descriptors
In addition to the concerns described above when delivering segmentation
descriptors over VANC the following should also be taken into consideration:
● Poor support within available SCTE-104 VANC inserters to include multiple
segmentation descriptors within a single SCTE-104 message
● The maximum size of a single SCTE-104 message is 2000 bytes in length
(​SMPTE.ST2010.2008​ - multi-operation message). This may in the long
term be limiting, if all markers had to be in one SCTE-104 message.
3.2.2. Single message per frame
When using VANC to deliver SCTE-104, a specific frame can only contain one
payload i.e SCTE-104 message. This means that if there is a requirement to signal
multiple markers at the same time point, then they must all have different pre-roll
times which results in different SCTE-104 message insertion times.

3.3. SCTE-104 over TCP/IP
Where the inserter (encoder) receives SCTE-104 commands over the TCP/IP
connection, there are a couple of ways in which the management of SCTE-
messages can be handled.
● The source of the SCTE-104 messages determines what messages to send
based on the broadcast channel that the encoder services
● As per SCTE-104 over VANC
With the first option there is no need of standardisation, and therefore is not
discussed further.

4. Generic capabilities
    The following sections describe the required capabilities of an Inserter irrespective
    of how the SCTE-104 is delivered.
4.1. PID mapping
Today most inserter (encoder) implementations don’t support the use of the
SCTE-104 DPI_PID_Index to determine the MPEG-PID that is used for the
corresponding outgoing SCTE-35 component. Typically it is assumed that there is a
single DPI_PID_index (with a value of 0) and they process all SCTE-104 message
from that single DPI_PID_index.
This is no longer adequate and so we would like to encourage encoder vendors to
fully implement the ability to support multiple SCTE-104 DPI_PID_Indexes, as
follows:
● Enable mapping of PDI_PID_Index to MPEG-PIDs via configuration.
● Allow multiple PDI_PID_Indexes to map to the same MPEG-PID.
● Allow message aggregation as defined below.
4.2. Message Aggregation
As has been discussed it is desirable form an SCTE-104 perspective to support
multiple SCTE-104 flows (using DPI_PID_index). However if there is considered a
need to support an SCTE-35 message containing multiple segmentation
descriptors, then the aggregation of the messages could be performed by the
inserter (encoder).
4.3. SCTE-104 processing configurations
The design shall allow an Inserter (encoder) to be configured with the set of
SCTE-104 messages that it should process and also how it should process them.

```
Ignore
Signal
Generate
SCTE-
Stream
Condition
Comments
No No Yes Encoder will perform stream conditioning
but will not insert an SCTE-35 Splice_insert
message.
Yes NA NA No action by encoder
No Yes Yes Encoder will perform stream conditioning
and will insert an SCTE-35 Splice_insert
message.
No Yes No Encoder will not perform stream
conditioning, but will insert an SCTE-
message.
No No No Encoder may perform some internal action,
but will not generate an SCTE-35 message.
(e.g. Modify Subtitle delay)
Enabling the above allows a broadcaster to incrementally roll-out
SCTE104/SCTE-35 messages across its broadcast channels, where a single source
feed (containing SCTE-104s) is used as input into multiple inserters (encoders).
```
5. SCTE-104/35 Message Types
    The following sections define the SCTE-104/35 messages that shall be used for
    specific marker Use Cases.
    It should be noted that this is not an exhaustive list and the solution shall support
    sending messages of any type. In fact SCTE-104 messages can be used to send
    generic signalling to an Inserter by extending the SCTE-104 opcodes, and this
    should not be precluded (e.g. SubTitle delay signalling).
5.1. Splice_Insert
Spot and Break markers shall be signalled using SCTE-35 Splice Insert messages,
with a duration field. Typically only a “from network” message will be sent, as the
duration field within this message is sufficient to determine the “to network” point.
An optional Avail descriptor may be included within the Splice Insert message.
In addition to the Splice Insert message an optional time_signal message with a
segmentation descriptor signalling a Spot/break marker may be sent. However this
is seen as secondary to the Splice Insert message.

5.2. Time_Signal
To signal Programme and Chapter boundaries a SCTE-35 segmentation descriptor
shall be used. This shall be carried within a time_signal message.
Support of segmentation descriptors within the Splice_insert and Splice_Null
message is not considered appropriate.
5.3. Splice_Null Messages
The inserter (Encoder) shall periodically generate an Splice Null message when
there are no actual SCTE-35 messages to be delivered. This is used to provide a
heartbeat to the mux, so that it does not alarm.
