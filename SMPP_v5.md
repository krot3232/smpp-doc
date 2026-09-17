# Short Message Peer-to-Peer Protocol Specification

## Version 5.0

#### 1 Introduction

The SMS Forum, a non-profit organisation dedicated to the promotion of SMS within the wireless industry, manages the Short Message Peer to Peer (SMPP) protocol. The specification and related documentation is available from the SMS Forum Website [http://www.smsforum.net](http://www.smsforum.net)

SMPP is an open, industry standard protocol designed to provide a flexible data communications interface for the transfer of short message data between External Short Message Entities (ESME), Routing Entities (RE) and Message Centres.

A Message Centre (MC) is a generic term used to describe systems such as a Short Message Service Centre (SMSC), GSM Unstructured Supplementary Services Data (USSD) Server, or Cell Broadcast Centre (CBC).

A ESME typically represents a fixed network SMS client, such as a WAP Proxy Server, E- Mail Gateway, or Voice Mail Server. It may also represent a Cell Broadcast Entity (CBE).

A Routing Entity (RE) is a generic term for a network element that is utilized for MC to MC, and ESME to MC message routing. A RE has the ability to emulate the functionality associated with both a MC and an ESME. To an ESME, a RE appears as a MC and to a MC, a RE appears as an ESME. A carrier may utilise REs to hide a network of Message Centres, presenting only the REs as the external interface point for ESMEs.

The following diagram illustrates the context of SMPP in a mobile network:
```text
┌─ ESME ──────────────────────┐         ┌─ Routing Entities ──────────┐
│       Mobile Commerce       │         │          ┌──────┐           │
├─────────────────────────────├─ SMPP ──┤          │  RE  │           │
│        Mobile Banking       │         │          └───┬──┘           │
├─────────────────────────────┤         │              │ SMPP         │
│        E-Mail Gateway       │         │              │              │
├─────────────────────────────┤         │          ┌───┴──┐           │
│     Information Services    │         │          │  RE  │           │
├─────────────────────────────┤         │          └──────┘           │
│  Notification Applications  │         └──────────────┬──────────────┘
├─────────────────────────────┤                        │
│      Directory Services     │                        │ SMPP
├─────────────────────────────┤                        │
│    Telemetry Applications   │                        │
├─────────────────────────────┤                        │
│   Vehicle Tracking Systems  │                        │
├─────────────────────────────┤                        │
│ Cell Broadcast Applications │                        │
└─┬───────────────────────────┘                        │
  └───────┬───────────────────────┬────────────────────┴──┐
   SMPP   │                       │                       │
     ┌────┴────┐             ┌────┴────┐       ┌──────────┴──────────┐
     │  SMSC 1 │             │  SMSC N │       │ Other (eg USSD, CBC)│
     └────┬────┘             └────┬────┘       └──────────┬──────────┘
          └───────────────────────┼───────────────────────┘
                                  │
             ┌─ Mobile Network ───┴────────────────────┐
             │  ┌─────┐        ┌─────┐        ┌─────┐  │
             │  │ MSC ├────────┤     ├────────┤ BSC │  │
             │  └─────┘        │ SS7 │        └─────┘  │
             │  ┌─────┐        │     │        ┌─────┐  │
             │  │ HLR ├────────┤     ├────────┤ VLR │  │
             │  └─────┘        └─────┘        └─────┘  │
             └────────────────────┬────────────────────┘
                                  │
                        ┌─────────┴─────────┐
                        │   Mobile devices  │
                        └───────────────────┘
```
**Figure 1-1 SMPP Network Diagram**

SMPP V5.0  SMS Forum 12 of 166

##### 1.1 Scope Of This Document

This document defines Version 5.0 of the SMPP protocol. It is intended for designers and implementers of a SMPP v5.0 interface between Message Centres, Routing Entities (RE) and External Short Message Entities (ESME).

##### 1.2 Glossary

|Term|Definition|
|---|---|
|ACK|Acknowledgement|
|API|Application Programming Interface|
|BTS|Base Transceiver Station|
|CBE|Cell Broadcast Entity, an ESME specifically designed for Cell Broadcast|
|CDR|Call Detail Record|
|ESME|External Short Message Entity.|
|ETSI|European Telecommunications Standards Institute|
|HEADER|Leading portion of the SMPP message, common to all SMPP PDUs|
|MB|Message Bureau-This is typically an operator message bureau.|
|MC|Message Centre-A generic term used to describe various types of SMS Gateways.|
|MCC|Mobile Country Code|
|MNC|Mobile Network Code|
|MS|Mobile Station|
|MSB|Most Significant Byte|
|MSC|Mobile Switching Centre|
|MWI|Message Waiting Indication|
|NACK|Negative Acknowledgement|
|NSAP|Network Service Access Point|
|PDU|Protocol Data Unit|
|PSSD|Process Unstructured Supplementary Services Data|
|PSSR|Process Unstructured Supplementary Services Request|
|RE|Routing Entity|
|SME|Short Message Entity|
|SMPP|Short Message Peer to Peer Protocol|
|SMSC|Short Message Service Centre|
|TIA|Telecommunications Industry Association|
|TLV|Tag/Length/Value. New style format for SMPP fields used to enhance existing PDUs with new features. Also known as Optional Parameter|
|UDHI|User Data Header Indicator|
|URL|Uniform Resource Locator|
|USSN|Unstructured Supplementary Services Notification|
|USSR|Unstructured Supplementary Services Request|
|VMA|Voicemail Alert|
|VPS|Voice Processing System|
|WAP|Wireless Application Protocol ([http://www.wapforum.org](http://www.wapforum.org))|
|WCMP|Wireless Control Message Protocol|
|WDP|Wireless Datagram Protocol|

**Table 1-1 Glossary**

SMPP V5.0  SMS Forum 13 of 166

##### 1.3 References
| Ref. | Document Title | Document Number | Version Number |
| :--- | :--- | :--- | :--- |
| [GSM 03.03] | Digital Cellular Telecommunications System (Phase 2+); Numbering, Addressing and Identification (Release 1998) | GSM 03.03 http://www.etsi.fr 3GPP http://www.3GPP.org | v7.8.0 |
| [GSM 03.38] | "Digital Cellular telecommunications system (Phase 2+); Alphabets and language specific information". | [GSM 03.38] http://www.etsi.fr Superseded by | v5.6.1 Jan. '98 |
| [GSM 03.40] | Technical Realisation of the Short Message Service Point to Point | GSM 03.40 http://www.etsi.fr Superseded by | v5.7.1 |
| [GSM MAP 09.02] | GSM Mobile Application Part | [GSM MAP 09.02] http://www.etsi.fr Superseded by | v5.11.0 |
| [IS637] | Short Message Service for Spread Spectrum Systems | TIA/EIA/IS-637-A | Rev A |
| [IS824] | Generic Broadcast Teleservice Transport Capability: Network Perspective. | TIA/EIA/IS-824 | For inclusion in IS41 Rev F |
| [IS630] | Broadcast Teleservice Transport -Broadcast Air Interface Transport (BATS). | TIA/EIA/IS-136-630 | Aug 31st 1999 ANSI TIA/EIA-136 Rev.A |
| [TSAR] | Teleservice Segmentation and Reassembly (TSAR) | TIA/EIA/136-620 | Rev 0 |
| [CMT-136] | Short Message Service - Cellular Messaging Teleservice | TIA/EIA-136-710-A | Rev A |
| [GUTS] | General UDP Transport Service (GUTS) | TIA/EIA-136-750 | Rev 0 |
| [ANSI-41] | Cellular Radio-Telecommunications Intersystem Operations | TIA/EIA-41.D | Rev D |
| [TSB29-D] | International Implementation of Wireless Telecommunications Systems | TIA/EIA | Rev D December 2000 |
| [WAPARCH] | Wireless Application Protocol Architecture Specification | WAP Forum http://wapforum.org | Version 30-Apr.-1998 |
| [WCMP] | Wireless Control Message Protocol Specification | WAP Forum http://wapforum.org | Version 4-Aug-1999 |
| [WDP] | Wireless Datagram Protocol Specification | WAP Forum http://wapforum.org | Version 5-Nov-1999 |
| [ITUT X.213] | Open Systems Interconnection - Network Service Definition | [ITUT X.213] | 11/95 |
| [KOR ITS] | PCS operators common standards for handset-SMS functionalities | PCS standardization committee PCS-SMS-97-05-28 | 1.06 Rev 99-04-30 |
| [3GPP TS 23.032] | Universal Geographical Area Description (GAD) (Release 4) | 3GPP http://www.3gpp.org | Version 4.0.0 |
| [3GPP TS 23.038] | Alphabets and language-specific information (Release 4) | 3GPP http://www.3gpp.org | Version 5.0.0 |
| [3GPP TS 23.040] | Technical Realization of the Short Message Service (SMS) (Release 4) | 3GPP http://www.3gpp.org | Version 5.3.0 |
| [3GPP TS 23.041] | Technical Realization of Cell Broadcast Service. (Release 4) | 3GPP http://www.3gpp.org | Version 4.2.0 |
| [3GPP TS 23.049] | Example protocol stacks for interconnecting Cell Broadcast Centre (CBC) and Base Station Controller (BSC). (Release 1998) | 3GPP http://www.3gpp.org | Version 7.0.0 |
| [3GPP TS 29.002] | Mobile Application Part (MAP) Specification (Release 4) | 3GPP | Version 4.6.0 |

**Table 1-2 References**

##### 1.4 SMPP Overview

The following sub-sections overview the basic concepts and characteristics of SMPP. Many of these characteristics will be discussed in greater detail throughout the document.

SMPP V5.0  SMS Forum 16 of 166

##### 1.4.1 Protocol Versions

Several versions of the SMPP protocol exist. The following table explains the differences between each version:

| Protocol Version | Owner | Description |
| :--- | :--- | :--- |
| V3.4 1999 | SMS Forum | This version of SMPP is based on V3.3 and was intended to provide a fully backward compatible specification to V3.3. The differences are summarized as follows: Removal of Query_last_messages operation;Removal of Query_message_details operation;Removal of Param_retrieve operation;Addition of Outbind operation to provide support for MC-initiated sessions.;Addition of data_sm operation to provide support for a lightweight submission operation applicable to WAP.;Addition of message modes to provide datagram and transaction mechanisms for message submission.;Error code set rationalized to remove vendor specific characteristics and make the protocol more applicable as an open interface.;Optional Parameters (TLVs) introduced as a means of extending PDUs in a backward compatible manner.;Protocol ownership transferred in full to the SMS Forum (formerly SMPP Developers Forum); |
| V4.0 1997 | Logica Aldiscon Ltd. (Logica plc.) | This version of SMPP was an independent version of the protocol designed and customized for the PDC market in Japan. It introduced features such as optional parameters, user message references and Outbind. The protocol remains proprietary and fully owned by Logica plc. |
| SMPP-P V1.0 - V1.3 1994-1997 | Aldiscon Ltd. (Logica plc.) | This was a set of protocol extensions designed to provide subscriber and distribution list provisioning services. |
| V1.0 - V3.3 1991-1997 | Aldiscon Ltd. (Logica plc.) | The original specification as defined and owned by Aldiscon Ltd. (now Logica). Version 1.0 of this protocol also features in GSM 03.39 version 4.0.0 ETR 243: November 1995. |

**Table 1-3 SMPP Protocol Versions**

##### 1.4.2 Supported Cellular Technologies

SMPP is designed to support short messaging functionality for any cellular technology and has specific applications and features for technologies such as:

- GSM
- UMTS
- IS-95 (CDMA)
- CDMA2000 (1xRTT & 3xRTT)
- ANSI-136 (TDMA)
- iDEN

##### 1.4.3 Typical Applications of SMPP

The variety of messaging applications, particularly SMS for which SMPP can be employed, is almost boundless. Wireless Operators, Message Centre vendors, Infrastructure Providers, and application developers are constantly developing new applications for SMS. SMPP is ideal as an access protocol for these applications. The following summarises common applications of SMPP:

- Voicemail alerts originating from a VPS (Voice Processing System), indicating voice messages at a customer’s mailbox. This is arguably one of the first ESME-based applications of SMS and is still heavily used in the industry.
- Numeric and alphanumeric paging services. With an SMS-capable phone, the need to carry both pager and phone is drastically reduced.
- Information services. For example, an application that enables mobile subscribers to query currency rates or share-price information from a database or the WWW and have it displayed as a short message on the handsets.
- Calls directly dialled or diverted to a message-bureau operator, who forwards the message to the MC, for onward delivery to a subscriber’s handset.
- Directory services. For example a subscriber calls a directory service requesting information on restaurants in a given area. The operator lists out available restaurants and sends the appropriate information as an SMS to the caller.
- Location-based services. These include applications that use mobile hardware to send GPS or cell data across SMS and using a MC, relay these messages to an ESME. The ESME may then use the collected data to manage services such as taxi assignment, stolen vehicle tracking and logistics control.
- Telemetry applications. For example, a household meter that transmits a short message to a utility company’s billing system to automatically record customer usage.
- Security applications such as alarm systems that can use SMS services for remote access and alerting purposes. For example, a parent receives an SMS from his security company to inform him that his daughter has arrived home and keyed in her access code.
- WAP Proxy Server. A WAP Proxy Server acts as the WAP gateway for wireless Internet applications. A WAP Proxy Server may select an SMS or USSD bearer for sending WDP datagrams to and receiving WDP datagrams from a mobile station.
- Online Banking, Share Dealing and E-Commerce, A mobile user could use SMS to send messages to an ESME requesting the purchase of products, shares etc. Likewise, a subscriber may use SMS to access banking services such as bill payment and funds transfer.
- Gaming and SMS Chat. Mobile users can interact with each other by means of a central server (ESME) and use this interaction as a means of playing wireless games, dating or SMS chat services similar to the concept of instant messaging and Internet room. These services have already appeared in the form of SMS-TV and SMS-Radio services.
- MMS Notification. In Multimedia Messaging, SMS is a bearer for the Multimedia Message Notification, which informs the recipient MMS user agent that a multimedia message is available on the Multimedia Message Centre.
SMPP V5.0  SMS Forum 19 of 166

- Cell Broadcast Services. Applications designed to support geographical messaging such as traffic alerts and emergency services, may use the Cell Broadcast features of SMPP to upload messages for periodic broadcast to subscribers within a given location.

##### 1.4.4 SMPP Sessions

In order to make use of the SMPP Protocol, a SMPP session must be established between the ESME and Message Centre or SMPP Routing Entity where appropriate. The established session is based on an application layer TCP/IP or X.25 connection between the ESME and MC/RE and is usually initiated by the ESME.

There are three forms of ESME-initiated session:

- Transmitter (TX) when authenticated as a transmitter, an ESME may submit short messages to the MC for onward delivery to Mobile Stations (MS). A transmitter session will also allow an ESME cancel, query or replace previously submitted messages. Messages sent in this manner are often called mobile terminated messages.
- Receiver (RX) A receiver session enables an ESME to receive messages from a MC. These messages typically originate from mobile stations and are referred to as mobile originated messages.
- Transceiver (TRX) A TRX session is a combination of TX and RX, such that a single SMPP session can be used to submit mobile terminated messages and receive mobile originated messages. Additionally, the Message Centre can establish a SMPP session by connecting to the ESME. This is referred to as an Outbind Session.
SMPP V5.0  SMS Forum 20 of 166

##### 1.4.5 Protocol Operations and PDUs

The SMPP protocol is basically a set of operations, each one taking the form of a request and response Protocol Data Unit (PDU). For example, if an ESME wishes to submit a short message, it may send a *submit_sm* PDU to the MC. The MC will respond with a *submit_sm_resp* PDU, indicating the success or failure of the request. Likewise, if a MC wishes to deliver a message to an ESME, it may send a *deliver_sm* PDU to an ESME, which in turn will respond with a *deliver_sm_resp* PDU as a means of acknowledging the delivery.

Some operations are specific to an ESME with others specific to the MC. Others may be specific to a given session type. Referring to the *submit_sm* and deliver_sm examples above, an ESME may send a submit_sm to a MC only if it has established a TX or TRX session with that Message Centre. Likewise, a MC may send *deliver_sm* PDUs only to ESMEs that have established RX or TRX sessions.

Operations are broadly categorised into the following groups:

- Session Management These operations are designed to enable the establishment of SMPP sessions between an ESME and MC and provide means of handling unexpected errors.
- Message Submission These operations are designed specifically for the submission of messages from ESME(s) to the MC.
- Message Delivery These operations enable a MC to deliver messages to the ESME.
- Message Broadcast These operations are designed to provide Cell Broadcast service within a Message Centre.
- Ancillary Operations These operations are designed to provide enhanced features such as cancellation, query or replacement of messages.
The following sub-sections list each category and its associated operations.

##### 1.4.5.1 Session Management Operations

| SMPP PDU Name | Description |
| :--- | :--- |
| `bind_transmitter` | Authentication PDU used by a transmitter ESME to bind to the Message Centre. The PDU contains identification information and an access password for the ESME. |
| `bind_transmitter_resp` | Message Centre response to a bind_transmitter PDU. This PDU indicates the success or failure of the ESME's attempt to bind as a transmitter |
| `bind_receiver` | Authentication PDU used by a receiver ESME to bind to the Message Centre. The PDU contains identification information, an access password for the ESME and may also contain routing information specifying the range of addresses serviced by the ESME. |
| `bind_receiver_resp` | Message Centre response to a bind_receiver PDU. This PDU indicates the success or failure of the ESME's attempt to bind as a receiver |
| `bind_transceiver` | Authentication PDU used by a transceiver ESME to bind to the Message Centre. The PDU contains identification information, an access password for the ESME and may also contain routing information specifying the range of addresses serviced by the ESME. |
| `bind_transceiver_resp` | Message Centre response to a bind_transceiver PDU. This PDU indicates the success or failure of the ESME's attempt to bind as a transceiver |
| `outbind` | Authentication PDU used by a Message Centre to Outbind to an ESME to inform it that messages are present in the MC. The PDU contains identification, and access password for the ESME. If the ESME authenticates the request, it will respond with a bind_receiver or bind_transceiver to begin the process of binding into the MC. |
| `unbind` | This PDU can be sent by the ESME or MC as a means of initiating the termination of a SMPP session. |
| `unbind_resp` | This PDU can be sent by the ESME or MC as a means of acknowledging the receipt of an unbind request. After sending this PDU the MC typically closes the network connection. |
| `enquire_link` | This PDU can be sent by the ESME or MC to test the network connection. The receiving peer is expected to acknowledge the PDU as a means of verifying the test. |
| `enquire_link_resp` | This PDU is used to acknowledge an enquire_link request sent by an ESME or MC. |
| `alert_notification` | A MC sends an alert_notification to an ESME as a means of alerting it to the availability of an SME. |
| `generic_nack` | This PDU can be sent by an ESME or MC as a means of indicating the receipt of an invalid PDU. The receipt of a generic_nack usually indicates that the remote peer either cannot identify the PDU or has deemed it an invalid PDU due to its size or content. |

***Table 1-4 Session Management Operations*** 

##### Message Submission Operations


| SMPP PDU Name | Description |
| :--- | :--- |
| `submit_sm` | A transmitter or transceiver ESME, wishing to submit a short message, can use this PDU to specify the sender, receiver and text of the short message. Other attributes include message priority, data coding scheme, validity period etc. |
| `submit_sm_resp` | The MC response to a submit_sm PDU, indicating the success or failure of the request. Also included is a MC message_id that can be used in subsequent operations to query, cancel or replace the contents of an undelivered message. |
| `submit_multi` | A variation of the submit_sm PDU that supports up to 255 recipients for the given message. |
| `submit_multi_resp` | The MC response to a submit_multi PDU. This is similar to the submit_sm_resp PDU. The main difference is that where some of the specified recipients were either invalid or rejected by the Message Centre, the PDU can specify the list of failed recipients, appending a specific error code for each one, indicating the reason the recipient was invalid. Also included is a MC message_id that can be used in subsequent operations to query, cancel or replace the contents of an undelivered message. |
| `data_sm` | data_sm is a streamlined version of the submit_sm operation, designed for packet-based applications that do not demand extended functionality normally available in the submit_sm operation. ESMEs implementing WAP over a SMS bearer typically use this operation. |
| `data_sm_resp` | The MC response to a data_sm PDU, indicating the success or failure of the request. Also included is a MC message_id that can be used in subsequent operations to query, cancel or replace the contents of an undelivered message. |

**Table 1-5 Message Submission Operations**

##### 1.4.5.3 Message Delivery Operations
| SMPP PDU Name | Description |
| --- | --- |
| `deliver_sm` | `Deliver_sm` is the symmetric opposite to `submit_sm` and is used by a MC to deliver a message to a receiver or transceiver ESME. |
| `deliver_sm_resp` | This PDU indicates the ESMEs acceptance or rejection of the delivered message. The error returned by the ESME can cause the message to be retried at a later date or rejected there and then. |
| `data_sm` | `Data_sm` can also be used for message delivery from Message Centre to the ESME. ESMEs implementing WAP over SMS typically use this operation. |
| `data_sm_resp` | The ESME response to a `data_sm` PDU, indicating the success or failure of the MC-initiated delivery request. |

**Table 1-6 Message Delivery Operations**

##### 1.4.5.4 Message Broadcast Operations
| SMPP PDU Name | Description |
| --- | --- |
| `broadcast_sm` | A broadcast ESME, wishing to broadcast a short message, can use this PDU to specify the alias, geographical areas, and text of the short message. |
| `broadcast_sm_resp` | The MC response to a `broadcast_sm` PDU, indicating the success or failure of the request. Also included is a MC `message_id` that can be used in subsequent operations to query or cancel the message. |

**Table 1-7 Message Broadcast Operations**

##### 1.4.5.5 Anciliary Submission Operations
| SMPP PDU Name | Description |
| --- | --- |
| `cancel_sm` | This PDU is used to cancel a previously submitted message. The PDU contains the source address of the original message and the `message_id` returned in the original `submit_sm_resp`, `submit_multi_resp` or `data_sm_resp` PDU. This PDU may also omit the `message_id` and instead contain a source address, destination address and optional `service_type` field as a means of cancelling a range of messages sent from one address to another. |
| `cancel_sm_resp` | The MC returns this PDU to indicate the success or failure of a `cancel_sm` PDU. |
| `query_sm` | This PDU is used to query the state of a previously submitted message. The PDU contains the source address of the original message and the `message_id` returned in the original `submit_sm_resp`, `submit_multi_resp` or `data_sm_resp` PDU. |
| `query_sm_resp` | The MC returns a `query_sm_resp` PDU as a means of indicating the result of a message query attempt. The PDU will indicate the success or failure of the attempt and for successful attempts will also include the current state of the message. |
| `replace_sm` | The `replace_sm` PDU is used by an ESME to pass a `message_id` of a previously submitted message along with several other fields used to update the text, validity period and other attributes of the message. |
| `replace_sm_resp` | The `replace_sm_resp` PDU indicates the success or failure of a `replace_sm` PDU |

**Table 1-8 Anciliary Submission Operations**

##### 1.4.5.6 Anciliary Broadcast Operations
| SMPP PDU Name | Description |
| --- | --- |
| `cancel_broadcast_sm` | This PDU is used to cancel the state of a previously broadcast message. |
| `cancel_broadcast_sm_resp` | Response to `cancel_broadcast_sm` PDU. |
| `query_broadcast_sm` | This PDU is used to query the state of a previously broadcast message. The PDU contains the source address of the original message and the `message_id` returned in the original `broadcast_sm_resp` PDU. |
| `query_broadcast_sm_resp` | The MC returns a `query_broadcast_sm_resp` PDU as a means of indicating the result of a broadcast query attempt. The PDU will indicate the success or failure of the attempt and for successful attempts will also include the current state of the message. |

**Table 1-9 Anciliary Broadcast Operations**

SMPP V5.0  SMS Forum 24 of 166

#### 2 SMPP Sessions

As described earlier, the ESME and MC communication is based on a SMPP session. This section describes this in detail.

##### 2.1 Application Layer Communication

The Open Systems Interconnect model or OSI stack as it is more commonly known, defines a layered approach to data communications working from the most basic electronic data communications (physical), up to link level communication involving the transmission of octets and onwards to fully formed network packets (network) and ultimately to transport layers that manage packets and the reliable transport of data, ensuring the appropriate resending of packets that are not properly received at the remote end.

SMPP is an application layer protocol, using the same underlying communications as protocols such as well known services like telnet, ftp, http etc. An application layer connection is typically presented as a buffer through which an application can send and receive data. The transport of this data between one peer and another is completely hidden from the ESME or MC. In fact, nowhere in SMPP is there any support for parity, CRC checking or any other form of corruption detection .This is all automatically handled by the application layer functionality of TCP/IP or X.25. The only assumption made by a SMPP-based application, ESME or MC, is that the remote peer conforms to the protocol and that a PDU sent from one peer to another is fully recognisable as a SMPP PDU.
```text
┌ ESME ──────────┐                              ┌ Message Center ┐
│  Application   │                              │  Application   │
├────────────────┤                              ├────────────────┤
│   Transport    │<─ Reliable Packet Transfer ─>│   Transport    │
├────────────────┤                              ├────────────────┤
│    Network     │<───── Packet Transfer ──────>│    Network     │
├────────────────┤                              ├────────────────┤
│      Data      │<────── Octet Transfer ──────>│      Data      │
├────────────────┤                              ├────────────────┤
│    Physical    │                              │    Physical    │
└───────┬────────┘                              └───────┬────────┘
        ▲                                               ▲
        │                                               │
        └───────────────────────────────────────────────┘
         Network Cable, Serial Link, Wireless Interface
```
**Figure 2-1 Application Layer Communication Between ESME and MC**

SMPP V5.0  SMS Forum 25 of 166

##### 2.2 Establishing a SMPP Session

Establishing a Session first requires the ESME to connect to the MC. This is achieved using a TCP/IP or X.25 connection. The MC will typically be listening for connections on one or more TCP/IP ports or X.25 interfaces (X.25 programmatic interfaces, DTE addresses and

X.25 protocol Ids). For TCP/IP, IANA has standardised port 2775 for SMPP. However ports may vary across MC vendors and operators.

##### 2.3 Session States

As already described, an ESME begins a session by connecting to the MC across TCP/IP or

X.25. This connection is referred to as a SMPP session and can have several states:

##### 2.3.1 Open

An ESME has established a network connection to the MC but has not yet issued a Bind request. The MC is only aware of the TCP/IP or X.25 connection. No identification details have yet been exchanged.
```text
                           Initiated by ESME
    ┌──────┐                                         ┌────────────────┐
    │ ESME │                                         │ Message Center │
    └───┬──┘                                         └────────┬───────┘
        │──────────────── Network Connection ────────────────>│
```
**Figure 2-2 Open State**

##### 2.3.2 Bound_TX
 A connected ESME has requested to bind as a Transmitter (by issuing a bind_transmitter_resp request. An ESME bound as a transmitter may send short messages to a MC for onward delivery to a Mobile Station or to another ESME. The ESME may also replace, query or cancel a previously submitted short message. Refer to section 2.4 for a full list of applicable operations in Bound_TX state.
```text
    ┌──────┐                                         ┌────────────────┐
    │ ESME │                                         │ Message Center │
    └───┬──┘                                         └────────┬───────┘
        │──────────────── Network Connection ────────────────>│
        │───────────────── bind_transmitter ─────────────────>│
        │<────────────── bind_transmitter_resp ───────────────│
```
**Figure 2-3 Bound_TX State**

##### 2.3.3 Bound_RX

A connected ESME has requested to bind as a Receiver (by issuing a *bind_receiver* PDU) and has received a *bind_receiver_resp* PDU from the MC authorising its Bind request. An ESME bound as a receiver may receive short messages from a MC, which may be originated, by a mobile station, by another ESME or by the MC itself (for example a MC delivery receipt). Refer to section 2.4 for a full list of applicable operations in Bound_RX state.
```text
    ┌──────┐                                         ┌────────────────┐
    │ ESME │                                         │ Message Center │
    └───┬──┘                                         └────────┬───────┘
        │──────────────── Network Connection ────────────────>│
        │─────────────────── bind_receiver ──────────────────>│
        │<─────────────── bind_receiver_resp ─────────────────│
```
**Figure 2-4 Bound_RX State**

##### 2.3.4 Bound_TRX

A connected ESME has requested to bind as a Transceiver (by issuing a *bind_transceiver* PDU) and has received a *bind_transceiver_resp* PDU from the MC authorising its Bind request. An ESME bound as a Transceiver is authorised to use all operations supported by a Transmitter ESME and a Receiver ESME. A transceiver session is effectively the combination of a Transmitter and a Receiver session.

Thus an ESME bound as a transceiver may send short messages to a MC for onward delivery to a Mobile Station or to another ESME and may also receive short messages from a MC, which may be originated by a mobile station, by another ESME or by the MC itself (for example a MC delivery receipt). Refer to section 2.4 for a full list of applicable operations in Bound_TRX state.
```text
    ┌──────┐                                         ┌────────────────┐
    │ ESME │                                         │ Message Center │
    └───┬──┘                                         └────────┬───────┘
        │──────────────── Network Connection ────────────────>│
        │───────────────── bind_transceiver ─────────────────>│
        │<────────────── bind_transceiver_resp ───────────────│
```
**Figure 2-5 Bound_TRX State**


##### 2.3.5 Unbound

An ESME bound as a TX, RX or TRX ESME has issued an *unbind* request to the MC requesting termination of the SMPP session. The MC may also issue an unbind request to the ESME. The receiving peer will then respond with an *unbind_resp* PDU acknowledging the request to end the session.

##### 2.3.6 Closed

The ESME or MC has closed the network connection. This typically results as follow-on to an Unbound state where one peer has requested termination of the session. Closed state can also result from either peer terminating the connection unexpectedly or due to a communications error within the underlying network that results in termination of the network connection.

##### 2.3.7 Outbound

The purpose of the *outbind* operation is to allow the MC initiate a SMPP session. A typical example of where such a facility might be applicable would be where the MC had outstanding messages for delivery to the ESME.
```text
                      Initiated by Message Center
    ┌──────┐                                         ┌────────────────┐
    │ ESME │                                         │ Message Center │
    └───┬──┘                                         └────────┬───────┘
        │<─────────────── Network Connection ─────────────────│
        │<───────────────────── outbind ──────────────────────│
```
**Figure 2-6 Outbound State**

The following diagram illustrates the concept of Outbind when used to request a receiver ESME to bind.
```text
    ┌──────┐                                         ┌────────────────┐
    │ ESME │                                         │ Message Center │
    └───┬──┘                                         └────────┬───────┘
        │<─────────────── Network Connection ─────────────────│
        │<───────────────────── outbind ──────────────────────│
        │─────────────────── bind_receiver ──────────────────>│
        │<─────────────── bind_receiver_resp ─────────────────│
```
**Figure 2-7 Bound_RX State from Outbound State**

##### 2.4 Operation Matrix

The following table lists each SMPP operation PDU by name and the appropriate Session states for its usage, indicating in terms of ESME and MC, which peer can issue the PDU.

**Note:** A SMPP Routing Entity (RE) is capable of emulating an ESME and MC at the same

time and therefore, all MC or ESME operations listed below are also simultaneously applicable to a RE. For example, a RE may issue a *bind_transmitter* to a MC while a session is in an open state (RE binding to Message Center). Additionally, the RE may return a *bind_transmitter_resp* PDU to an ESME with an open state session (ESME binding to RE).

|Session State|Open||Outbound||Bound_TX||Bound_RX||Bound_TRX||Unbound||
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|SMPP Entity PDU alert_notification bind_receiver bind_receiver_resp bind_transceiver bind_transceiver_resp bind_transmitter bind_transmitter_resp broadcast_sm broadcast_sm_resp cancel_broadcast_sm cancel_broadcast_sm_resp cancel_sm cancel_sm_resp|ESME • • •|MC • • •|ESME • • •|MC • • •|ESME • • •|MC • • •|ESME|MC •|ESME • • •|MC • • • •|ESME|MC|
|data_sm|||||•|||•|•|•|||
|data_sm_resp deliver_sm deliver_sm_resp||||||•|• •|•|• •|• •|||
|enquire_link|•|•|•|•|•|•|•|•|•|•|•|•|
|enquire_link_resp|•|•|•|•|•|•|•|•|•|•|•|•|
|generic_nack|•|•|•|•|•|•|•|•|•|•|•|•|
|outbind||•|||||||||||
|query_broadcast_sm|||||•||||•||||
|query_broadcast_sm_resp||||||•||||•|||
|query_sm|||||•||||•||||
|query_sm_resp||||||•||||•|||
|replace_sm|||||•||||•||||
|replace_sm_resp||||||•||||•|||
|submit_multi|||||•||||•||||
|submit_multi_resp||||||•||||•|||
|submit_sm|||||•||||•||||

SMPP V5.0  SMS Forum 29 of 166

|Session State|Open||Outbound||Bound_TX||Bound_RX||Bound_TRX||Unbound||
|---|---|---|---|---|---|---|---|---|---|---|---|---|
|SMPP Entity PDU submit_sm_resp|ESME|MC|ESME|MC|ESME|MC •|ESME|MC|ESME|MC •|ESME|MC|
|unbind|||||•|•|•|•|•|•|||
|unbind_resp|||||•|•|•|•|•|•|||

**Table 2-1 Operation Matrix**

SMPP V5.0  SMS Forum 30 of 166

##### 2.5 Sample Sessions

To help explain the context of SMPP operations and their related states, the following examples illustrate typical dialogues for the three types of ESME.

##### 2.5.1 Example Transmitter Session
```text
    ┌──────┐                                         ┌────────────────┐
    │ ESME │                                         │ Message Center │
    └───┬──┘                                         └────────┬───────┘
        │──────────────── Network Connection ────────────────>│
     [Open]                                                   │
        │───────────────── bind_transmitter ─────────────────>│
        │<────────────── bind_transmitter_resp ───────────────│
   [Bound_TX]                                                 │
        │───────────────────── submit_sm ────────────────────>│
        │<───────────────── submit_sm_resp ───────────────────│
        │───────────────────── submit_sm ────────────────────>│
        │<───────────────── submit_sm_resp ───────────────────│
        │───────────────────── cancel_sm ────────────────────>│
        │<───────────────── cancel_sm_resp ───────────────────│
        │────────────────────── unbind ──────────────────────>│
        │<─────────────────── unbind_resp ────────────────────│
    [Unbound]                                                 │
        │                                                     │
        │----------------- Connection Closed ---------------->│
    [Closed]                                                  │
```
**Figure 2-8 Example Transmitter Session**

SMPP V5.0  SMS Forum 31 of 166

##### 2.5.2 Example Receiver Session
```text
    ┌──────┐                                         ┌────────────────┐
    │ ESME │                                         │ Message Center │
    └───┬──┘                                         └────────┬───────┘
        │──────────────── Network Connection ────────────────>│
     [Open]                                                   │
        │─────────────────── bind_receiver ──────────────────>│
        │<─────────────── bind_receiver_resp ─────────────────│
   [Bound_RX]                                                 │
        │<─────────────────── deliver_sm ─────────────────────│
        │────────────────── deliver_sm_resp ─────────────────>│
        │<─────────────────── deliver_sm ─────────────────────│
        │────────────────── deliver_sm_resp ─────────────────>│
        │────────────────────── unbind ──────────────────────>│
        │<─────────────────── unbind_resp ────────────────────│
    [Unbound]                                                 │
        │                                                     │
        │----------------- Connection Closed ---------------->│
    [Closed]                                                  │
```
**Figure 2-9 Example Receiver Session**

SMPP V5.0  SMS Forum 32 of 166

##### 2.5.3 Example Transceiver Session
```text
    ┌──────┐                                         ┌────────────────┐
    │ ESME │                                         │ Message Center │
    └───┬──┘                                         └────────┬───────┘
        │──────────────── Network Connection ────────────────>│
     [Open]                                                   │
        │───────────────── bind_transceiver ─────────────────>│
        │<────────────── bind_transceiver_resp ───────────────│
   [Bound_TRX]                                                │
        │<─────────────────── deliver_sm ─────────────────────│
        │────────────────── deliver_sm_resp ─────────────────>│
        │<─────────────────── deliver_sm ─────────────────────│
        │────────────────── deliver_sm_resp ─────────────────>│
        │───────────────────── submit_sm ────────────────────>│
        │<───────────────── submit_sm_resp ───────────────────│
        │───────────────────── query_sm ─────────────────────>│
        │<────────────────── query_sm_resp ───────────────────│
        │────────────────────── unbind ──────────────────────>│
        │<─────────────────── unbind_resp ────────────────────│
    [Unbound]                                                 │
        │                                                     │
        │----------------- Connection Closed ---------------->│
    [Closed]                                                  │
```
**Figure 2-10 Example Transceiver Session**

SMPP V5.0  SMS Forum 33 of 166

##### 2.5.4 Example Transmitter Session (Cell Broadcast Entity)
```text
    ┌──────┐                                         ┌────────────────┐
    │ ESME │                                         │ Message Center │
    └───┬──┘                                         └────────┬───────┘
        │──────────────── Network Connection ────────────────>│
     [Open]                                                   │
        │───────────────── bind_transmitter ─────────────────>│
        │<────────────── bind_transmitter_resp ───────────────│
   [Bound_TX]                                                 │
        │─────────────────── broadcast_sm ───────────────────>│
        │<──────────────── broadcast_sm_resp ─────────────────│
        │─────────────────── broadcast_sm ───────────────────>│
        │<──────────────── broadcast_sm_resp ─────────────────│
        │──────────────── cancel_broadcast_sm ───────────────>│
        │<──────────── cancel_broadcast_sm_resp ──────────────│
        │────────────────────── unbind ──────────────────────>│
        │<─────────────────── unbind_resp ────────────────────│
    [Unbound]                                                 │
        │                                                     │
        │----------------- Connection Closed ---------------->│
    [Closed]                                                  │
```
**Figure 2-11 Example Transmitter Session (Cell Broadcast Entity)**

SMPP V5.0  SMS Forum 34 of 166

##### 2.5.5 Example Outbind Session

This example depicts an outbind session that results in the binding of a receiver ESME.
```text
    ┌──────┐                                         ┌────────────────┐
    │ ESME │                                         │ Message Center │
    └───┬──┘                                         └────────┬───────┘
        │<─────────────── Network Connection ─────────────────│
     [Open]                                                   │
        │<───────────────────── outbind ──────────────────────│
   [Outbound]                                                 │
        │─────────────────── bind_receiver ──────────────────>│
        │<─────────────── bind_receiver_resp ─────────────────│
   [Bound_RX]                                                 │
        │<─────────────────── deliver_sm ─────────────────────│
        │────────────────── deliver_sm_resp ─────────────────>│
        │<─────────────────── deliver_sm ─────────────────────│
        │────────────────── deliver_sm_resp ─────────────────>│
        │────────────────────── unbind ──────────────────────>│
        │<─────────────────── unbind_resp ────────────────────│
    [Unbound]                                                 │
        │                                                     │
        │----------------- Connection Closed ---------------->│
    [Closed]                                                  │
```
**Figure 2-12 Example Outind Session**

SMPP V5.0  SMS Forum 35 of 166

##### 2.6 PDU Sequencing

Up to now, we have referred to PDUs by name and indicated the request/response pairs in the form of the session diagrams. The impression can be formed that SMPP is a handshake protocol where each request is first acknowledged before issuing the next request. However this is not the case.

##### 2.6.1 The PDU Sequence Number

Each SMPP request PDU has an identifier called a sequence number that is used to uniquely identify the PDU in the context of its’ originating entity and the current SMPP session. The resulting response PDU (which must be returned on the same SMPP session) is expected to mirror the sequence number of the original request. The following diagram illustrates the use of sequence numbers.
```text
    ┌──────┐                                         ┌────────────────┐
    │ ESME │                                         │ Message Center │
    └───┬──┘                                         └────────┬───────┘
        │──────────────── Network Connection ────────────────>│
     [Open]                                                   │
        │───────────── bind_transceiver (seq=1) ─────────────>│
        │<────────── bind_transceiver_resp (seq=1) ───────────│
   [Bound_TRX]                                                │
        │<─────────────── deliver_sm (seq=1) ─────────────────│
        │────────────── deliver_sm_resp (seq=1) ─────────────>│
        │<─────────────── deliver_sm (seq=2) ─────────────────│
        │────────────── deliver_sm_resp (seq=2) ─────────────>│
        │───────────────── submit_sm (seq=2) ────────────────>│
        │<───────────── submit_sm_resp (seq=2) ───────────────│
        │───────────────── query_sm (seq=3) ─────────────────>│
        │<────────────── query_sm_resp (seq=3) ───────────────│
        │────────────────── unbind (seq=4) ──────────────────>│
        │<─────────────── unbind_resp (seq=4) ────────────────│
    [Unbound]                                                 │
        │                                                     │
        │----------------- Connection Closed ---------------->│
    [Closed]                                                  │
```
**Figure 2-13 Transceiver Session demonstrating PDU Sequencing**

Referring to the above example, sequence numbers are uniquely issued per request PDU. This means that for every PDU request issued by an ESME, it must use a different sequence number. The recommended approach is to use monotonically increasing sequence numbers, starting at 1. The first issued PDU request has a sequence number of 1, the next uses 2 and so on.

Each response PDU must carry the sequence number used in the matching request. In the above example, the MC responded with a *bind_transceiver_resp* carrying a sequence number of 1, simply because the *bind_transceiver* request had a sequence number of 1. The next request issued by the ESME was the *submit_sm* PDU and it carried a sequence number

SMPP V5.0  SMS Forum 36 of 166

of 2. The third and forth request PDUs to be sent by the ESME further incremented this value to 3 and 4 respectively.

At the same time, all *deliver_sm* requests issued by the MC use monotonically increasing numbers. These can very easily match those issued by the ESME. But the confusion is avoided by the context of a PDU. Basically every request must emanate from either the ESME or MC and the resulting response must emanate from the other party. So in effect, each SMPP session involves two sets of sequence numbers; the set used by the ESME for ESME-originated requests and the set used by the MC for MC-originated requests. They can cross over each other in value, but will or should not be confused with each other.

##### 2.6.2 Why use Monotonically Increasing Sequence numbers?

Monotonically increasing sequence numbers are easy to implement and handle. They also have the additional characteristic of providing a running operation count for the session. Thus for any session employing monotonically increasing sequence numbers, a sequence number th of 100, identifies the 100 PDU issued within the session.

While the recommendation is that sequence numbers should be monotonically increased, this is not a mandatory requirement and some ESMEs or MCs may deliberately select random numbers or values based on some predetermined sequence.

Another approach for an ESME or MC is to use a selected range of sequence numbers reusing values as soon as the associated response has been received.

Whatever the approach taken by the originating peer, the responding peer is expected to honour these values and use the same sequence number in the response PDU.

##### 2.6.3 Sequence Numbers Across Sessions

Sequence numbers are designed for use within a single session. The recommended approach is to begin each session with a sequence number of 1 and increase monotonically from that point.

The numbers used will not affect any other sessions that may already exist between the ESME and MC. Nor can PDUs from one session be acknowledged through another.

If the ESME-MC connection is closed or lost, then the expected recovery would be that the ESME or MC might re-establish the session. All unacknowledged PDUs from the lost session will not be acknowledged in the new recovery session, i.e. if the session was lost at a point where the MC had yet to send a *submit_sm_resp* to the ESME, then a new session established between the ESME and MC will not result in this response PDU being returned. Instead, the ESME is expected to begin numbering PDUs from 1. The same expectation applies for the Message Centre.

##### 2.6.4 Synchronous Vs. Asynchronous

SMPP is an asynchronous protocol. This means that an ESME or MC can send several requests at a time to the other party. The PDU sequence number plays a crucial role in supporting the asynchronous nature of SMPP. All example sessions shown in the previous sections have been synchronous. Here is an example of an asynchronous session.
```text
    ┌──────┐                                         ┌────────────────┐
    │ ESME │                                         │ Message Center │
    └───┬──┘                                         └────────┬───────┘
        │──────────────── Network Connection ────────────────>│
     [Open]                                                   │
        │───────────── bind_transmitter (seq=1) ─────────────>│
        │<────────── bind_transmitter_resp (seq=1) ───────────│
   [Bound_TX]                                                 │
        │───────────────── submit_sm (seq=2) ────────────────>│
        │───────────────── submit_sm (seq=3) ────────────────>│
        │───────────────── submit_sm (seq=4) ────────────────>│
        │───────────────── submit_sm (seq=5) ────────────────>│
        │───────────────── submit_sm (seq=6) ────────────────>│
        │<───────────── submit_sm_resp (seq=2) ───────────────│
        │───────────────── submit_sm (seq=7) ────────────────>│
        │───────────────── query_sm (seq=8) ─────────────────>│
        │<───────────── submit_sm_resp (seq=3) ───────────────│
        │<────────────── query_sm_resp (seq=8) ───────────────│
        │<───────────── submit_sm_resp (seq=4) ───────────────│
        │<───────────── submit_sm_resp (seq=7) ───────────────│
        │<───────────── submit_sm_resp (seq=5) ───────────────│
        │<───────────── submit_sm_resp (seq=6) ───────────────│
        │────────────────── unbind (seq=9) ──────────────────>│
        │<─────────────── unbind_resp (seq=9) ────────────────│
    [Unbound]                                                 │
        │                                                     │
        │----------------- Connection Closed ---------------->│
    [Closed]                                                  │
```
**Figure 2-14 Asynchronous Transmitter Session**

The asynchronous behaviour of both the ESME and Message Centre is evident in the above session. The ESME issues 5 *submit_sm* requests to the MC before receiving its first *submit_sm_resp*. Note that the order in which the MC acknowledges these requests is by no means guaranteed to match the transmission order of the original requests. A Message Centre, because of it using a distributed architecture or because it is in fact a SMPP Routing Entity and is distributing the ESME requests to other Message Centres, is likely to acknowledge requests in the order they are completed. Some requests may be distributed to busier parts of the system than others and as such may take longer to process. The result is that the asynchronous sequence of acknowledgements returned to the ESME may not carry the same order as used by the requests. The same applies for messages delivered by the MC to the ESME. The MC must support the ability to process response PDUs in non- contiguous order.

The PDU sequence numbers make the request/response matching possible. So regardless of when an asynchronous response arrives, it can be immediately re-matched to the original request PDU. Thus, if only for this reason, each PDU request should use a unique sequence number within the context of the session.

SMPP V5.0  SMS Forum 38 of 166

##### 2.6.5 Why Asynchronous?

Synchronous behaviour may appear the easier route to take when developing an application. Sending at most one PDU request, then waiting for the response is an easy alternative over the management of an entire window of PDUs, which may be acknowledged in a non- contiguous order.

However for an application to efficiently utilise the SMPP session bandwidth, asynchronous transmission can make significant improvements. The following diagram helps explain the reasoning for using the protocol in an asynchronous manner.
```text
    ┌──────┐                                         ┌────────────────┐
    │ ESME │                                         │ Message Center │
    └───┬──┘                                         └────────┬───────┘
        │   ┌───┐ ┌───┐ ┌───┐ ┌───┐ ┌───┐                     │
        │── │PDU│ │PDU│ │PDU│ │PDU│ │PDU│ ── Requests ───────>│
        │   └───┘ └───┘ └───┘ └───┘ └───┘                     │
        │<─────────── Network Connection ────────────────────>│
        │   ┌───┐ ┌───┐ ┌───┐ ┌───┐ ┌───┐                     │
        │<─ │PDU│ │PDU│ │PDU│ │PDU│ │PDU│ ── Responses ───────│
        │   └───┘ └───┘ └───┘ └───┘ └───┘                     │
```
**Figure 2-15 Asychronous Windowing**

The above example shows 5 PDUs asynchronously transmitted across the SMPP session from the ESME to MC. If the MC can process only one PDU at a time, then the benefit of asynchronous transmission is actually not lost. In this situation, the window of PDUs becomes a queue of requests for the MC. As soon as the MC acknowledges each request, it will immediately find another request waiting. The same benefits apply to receiver and transceiver sessions where the MC is issuing deliver_sm or data_sm PDUs to the ESME.

If the ESME is synchronous and can only send a single PDU at a time, then as soon as the MC acknowledges the PDU, the SMPP session will be idle until the response PDU has been processed by the ESME and the next request dispatched to the MC.

If we consider the transport time from ESME to MC to take α microseconds, then the overall idle time per operation is 2α. This is the elapsed idle time while the response is in transit to the ESME and when the next request is in transit to the MC. An asynchronous window of requests effectively avoids this inefficiency.

SMPP V5.0  SMS Forum 39 of 166

##### 2.7 Session Timers

SMPP operations are based on the exchange of operation PDUs between ESME and MC. In order to control the amount of time spent waiting for a response to arrive or particular operation to occur, the following timers are defined:

|Timer|Required|Action on expiration|Description|
|---|---|---|---|
||SMPP|||
||Session State|||

Session Init Timer Open The network connection This timer specifies the time Outbound should be terminated. lapse allowed between a network connection being established by an ESME and a *bind_transmitter*, *bind_receiver* or *bind_transceiver* request being sent to the MC.

The timer can also be used by a MC supporting Outbind and applied to the time interval between an Outbind request being sent to an ESME and its response with a bind request.

This timer can also be used by an ESME supporting Outbind where an ESME will close the MC-initiated connection within the defined period if the MC fails to send an Outbind request.

This timer should always be active on the MC and on <u>ESMEs supporting Outbind.</u>

|Open|An enquire_link request|This timer specifies the time|
|---|---|---|
|Unbound Outbound|should be initiated.|lapse allowed between operations after which a|
|Bound_TX||SMPP entity should|
|Bound_RX||interrogate whether its’ peer|
|Bound_TRX||still has an active session.|

Enquire Link Timer

This timer may be active on either peer (i.e. MC or ESME).

SMPP V5.0  SMS Forum 40 of 166

|Timer|Required|Action on expiration|Description|
|---|---|---|---|
||SMPP|||
||Session State|||

|Bound_TX|The SMPP session|This timer specifies the|
|---|---|---|
|Bound_RX Bound_TRX|should be dropped.|maximum time lapse allowed between|

##### Inactivity Timer

transactions, after which period of inactivity, a SMPP entity may assume that the session is no longer active. The resulting behaviour is to either close the session or issue an unbind request.

This timer may be active on either peer (i.e. MC or ESME).

|Open|Given operation|This timer specifies the time|
|---|---|---|
|Unbound|assumed to have failed.|lapse allowed between a|
|Outbound||SMPP request and the|
|Bound_TX||corresponding SMPP|
|Bound_RX||response.|
|Bound_TRX||This timer may be active on|

Response Timer

either communicating SMPP entity (i.e. MC or ESME).

**Table 2-2 SMPP Session Timers**

##### 2.8 Error Handling

This section addresses typical error scenarios that can occur and how an ESME or MC should go about handling such scenarios

##### 2.8.1 Handling Connection Failure

An ESME or MC may experience a failed connection attempt or may suddenly lose its connection to the other peer. This can be due to any of the following reasons.

- The IP address and port or X.25 details are incorrect (for failed connection attempts)
- The remote MC or ESME is down or unable to accept the connection
- The network between the two hosts is down
The recommended approach is to continually try to connect or reconnect again at intervals. Many ESME systems provide crucial services to an SMS network. If a network or Message Centre becomes unavailable, causing ESMEs to lose their SMPP connections, then as long as an ESME enters a mode whereby it attempts to reconnect at intervals of say five seconds, then as soon as the network or Message Centre service is restored, the ESME will be quickly reconnected to resume service.

Most SMPP sessions will be ESME-initiated, but as we have seen from Outbind, the MC can itself be in a position to connect to an ESME that is configured for Outbind. The likely reason for attempting an Outbind is that messages for the ESME have arrived in the MC. If the connection attempt fails, it is recommend that the MC use a similar mechanism of periodically attempting to Outbind to the ESME. This may be driven by a retry sequence that controls the frequency of delivery attempts made for a given message. Every time the ESMEs message is scheduled for retry, the MC attempts to Outbind to the ESME.

SMPP V5.0  SMS Forum 41 of 166

##### 2.8.2 Operation Failure

There are a number of reasons why a MC or ESME may reject an operation request PDU. These are:

- The PDU is unrecognised This is one of the most common reasons for rejecting a PDU. It can result from an ESME or MC sending a PDU that the receiving peer does not recognise. The typical response in this scenario is a *generic_nack* PDU being returned to the sender with the sequence number of the offending PDU. The command status is usually ESME_RINVCMDID, which indicates that the ESME or MC cannot recognise the PDU. In this situation the error is with the receiving peer and is effectively a non-compliance scenario. However some ESMEs and MCs will not support all operations and will usually provide this information in their SMPP PICS document.
- The PDU is malformed When this happens, it indicates that the sending entity is at fault for sending a non-standard PDU. Typical responses will depend on how the malformed PDU is detected. If the *command_id* is the reason for rejection, the receiving peer should respond with a *generic_nack* and command status set to ESME_RINVCMDID. If the *command_length* of the PDU appeared too large, suggesting that it was invalid, the ESME or MC should respond with a *generic_nack* and command status set to ESME_RINVCMDLEN.
- Invalid Field Length If any PDU field is too long or too short, then the PDU is essentially malformed, but an ESME or MC may indeed recognise the PDU and as such will respond with a *submit_sm_resp* or whatever is the appropriate PDU to use and set the command status to the appropriate error. For example, if an ESME submits a message with a 20 character scheduled delivery time, the rejection should be a *command_status* set to ESME_RINVSCHED
- The PDU data is unexpected and deemed invalid This type of rejection is an application rather than a protocol compliance issue and an absolute list of causes is well beyond the scope of this document. An example of such a scenario could be an Email gateway that provides a service to convert Mobile originated SMS messages into emails. The email addressing might be based on the text content of the message. If the ESME found that the message forwarded by the MC did not contain the correct formatting it would reject the message. The typical error code would be ESME_RX_R_APPN, which is a means of rejecting a message and ensuring that it is not retried at a later point.
- The PDU is not allowed in the current session state This is a violation of the rules of the SMPP sessions and a simple example would be an ESME in Bound_RX state, attempting to submit a message by sending a *submit_sm* PDU or by attempting to submit a message across an Open State session without first binding. The expected command status in the *submit_sm_resp* PDU would be ESME_RINVBNDSTS
- The ESME or MC is restricting the use of certain PDUs or features SMPP has a broad scope of functionality and some Message Centres or ESMEs may deliberately provide mechanisms to disable certain features. For example if an operator configured a message centre to reject attempts by ESMEs to request delivery receipts for messages, it would force the MC to reject the message with a command status set to ESME_RINVREGDLVFLG. Although the field may be correctly encoded, its usage is disabled and the MC is authorised to reject the message using that error code.
SMPP V5.0  SMS Forum 42 of 166

##### 2.9 Flow Control and Congestion Avoidance

It is a common misconception that windowing provides full flow control. However, as already discussed in section 2.6.4, all that is gained is a finite limit to an asynchronous window. A maxed out window prevents the originator from issuing more requests until responses arrive, but this is not flow control.

Flow control relates to the concept of a receiver informing the sender that it can’t accept any more data.

In TCP, this concept is supported by a ‘receiver buffer advertisement’, which can be passed with every packet acknowledgement. The sender uses this data as a means of judging how much more data can be sent in subsequent transmissions. This mechanism works fine for TCP, given that congestion is based mostly on volumes of data being sent across busy networks or to a congested receiver.

In terms of SMPP, if an ESME or MC submits/delivers messages at a rate that exceeds the capabilities of its peer, congestion may occur. Relying on windowing to solve the problem is not enough. The ESME will continue to top up its window of unacknowledged requests, keeping the MC under load to process these requests.

To better assist a peer (ESME or MC) in avoiding congestion, the peer needs a mechanism to provide the receiving peer with an indication of its state of congestion.

This is accomplished with the addition of an optional *congestion_state* TLV. This parameter may be optionally included in any response PDU sent between an ESME and MC. This TLV contains a simple integer from 0-100 to indicate the Congestion state ranging from idle to congested. Refer to 4.8.4.18 for details on the values acceptable for this TLV.

The ESME or MC can use this value as a means of detecting increased load scenarios on its peer and take the appropriate action to reduce its input rates. In order to get the maximum throughput from the transfer of messages, the MC or ESME should try to maintain the Congestion state between 80-90 (Optimum Load).

When commencing a SMPP session, the ESME or MC would begin transmission of requests, with a maximum window of N. If the ESME or MC supports the Congestion_State TLV, then as the responses arrive, the ESME/MC can increase/decrease its transmission rate according to the indicated Congestion state of its peer. If the Congestion_State TLV is supported, then the Window of N can actually be discarded as the Congestion_State itself acts as a better indicator and preventative measure of congestion avoidance.

If the TLV is not present in response PDUs, then simple linear windowing is the only means of applying flow control within the session.

The advantage of using *congestion_state* over a fixed window is that the ESME can avail of the optimum performance available at a particular time instead of predetermining some window limit and using this consistently. This recognises that a MC or ESME may be under varying levels of stress and that predetermined performance is not always guaranteed.
```text
       ┌──────┐                                  ┌────────────────┐
       │ ESME │                                  │ Message Center │
       └───┬──┘                                  └────────┬───────┘
      [Bound_TX]                                          │
   50/sec  │──────────────────── XXX ────────────────────>│
           │<────── XXX_resp (congestion_state=40) ───────│ [Medium]
   80/sec  │──────────────────── XXX ────────────────────>│
           │<────── XXX_resp (congestion_state=55) ───────│ [High]
           │<────── XXX_resp (congestion_state=72) ───────│ [High]
  120/sec  │──────────────────── XXX ────────────────────>│
           │<────── XXX_resp (congestion_state=85) ───────│ [Optimum]
  120/sec  │──────────────────── XXX ────────────────────>│
           │<────── XXX_resp (congestion_state=83) ───────│ [Optimum]
  120/sec  │──────────────────── XXX ────────────────────>│
           │<────── XXX_resp (congestion_state=86) ───────│ [Optimum]
           │<────── XXX_resp (congestion_state=88) ───────│ [Optimum]
  120/sec  │──────────────────── XXX ────────────────────>│
           │<────── XXX_resp (congestion_state=94) ───────│ [Nearing Congestion]
  100/sec  │──────────────────── XXX ────────────────────>│
  100/sec  │<────── XXX_resp (congestion_state=88) ───────│ [Optimum]
```
**Figure 2-16 Flow Control & Congestion Avoidance using the *congestion_state* TLV.**

The above diagram shows a session where an ESME is transmitting PDUs at a rate of 50/second. On recognising the *congestion_state* TLV and its below Optimum value, the ESME increased its rate until the *congestion_state* enters an optimum range. At this point, the ESME maintains the 120 PDUs/second until the *congestion_state* enters ‘Nearing Congestion’, at which time the ESME relaxes the messaging rate to return the *congestion_state* to an optimum level.

##### 2.10 Session Security and Encryption

SMPP itself, does not define any native encryption mechanisms. The content exchanged across a SMPP session is open to unauthorised interception or impersonation if transmitted across an open medium such as the Internet. To combat this risk, there are two recommended approaches to securing SMPP sessions.

SMPP V5.0  SMS Forum 44 of 166

##### 2.10.1 Leased Lines

A common approach to securing a session between ESME and MC is to use a leased line. This avoids using publicly accessible media such as the Internet and the risk of a security breach is reduced.

##### 2.10.2 Secure Transport Layer

The well known Secure Socket Layer (SSL) and its standardised form TLS provide an excellent means of securing a connection between two peers by using a variety of encryption ciphers and authentication certificates. SSL/TLS is the backbone of how E-commerce is managed and has proven itself very reliable. Many commercial and some open source libraries exist to facilitate the adaptation of SSL/TLS-based transport layers into applications. The ESME or MC, in supporting the SSL/TLS approach would connect using the SSL/TLS mechanism, first establishing a secure and authenticated connection before commencing the SMPP session.

##### 2.10.3 Secure VPN

A Virtual Private Network is typically used within organizations to consolidate intranets, extranets and other forms of networks. Security solutions within VPNs can vary from system to system. However the typical approach is to encrypt at the packet or data layers. The result is that an insecure session from one VPN to another can be transparently encrypted using a variety of encryption mechanisms. The ESME and MC need not support encryption directly.
```text
    ESME VPN                            Message Center VPN
┌────────────────────────┐        ┌──────────────────────────────┐
│  ┌──────┐              │        │ ┌────────────────┐           │
│  │ ESME │              │        │ │ Message Center │           │
│  └───┬──┘              │        │ └───────┬────────┘           │
│      ▲ Insecure        │        │         ▲ Insecure           │
│      ▼ SMPP Session    │        │         ▼ SMPP Session       │
│ ┌────┴─────────┐       │        │  ┌──────┴───────┐            │
│ │  VPN Router  │       │        │  │  VPN Router  │            │
│ └────┬─────────┘       │        │  └──────┬───────┘            │
│      ▲                 │        │         ▲                    │
└──────┼─────────────────┘        └─────────┼────────────────────┘
       └────── Secure Session ──────────────┘
```
**Figure 2-17 ESME-MC SMPP session using a secure VPN**

SMPP V5.0  SMS Forum 45 of 166

##### 2.10.4 Secure Tunnel

Another approach to securing a SMPP session is to use a secure tunnel. This is where the ESME-MC connection is not made directly from one peer to another but by connecting to a secure tunnel server. The tunnel server is configured to match a particular insecure connection from an ESME with a secure connection to another tunnel usually located in the peers network. The remote tunnel, then matches the incoming connection with another insecure connection onwards to the other peer. The result is that an ESME and MC combination unable to support direct SSL/TLS can still be secured for Internet ready communication by means of a tunnel server.
```text
    ESME Network                        Message Center Network
┌────────────────────────┐        ┌──────────────────────────────┐
│  ┌──────┐              │        │ ┌────────────────┐           │
│  │ ESME │              │        │ │ Message Center │           │
│  └───┬──┘              │        │ └───────┬────────┘           │
│      ▲ Insecure        │        │         ▲ Insecure           │
│      ▼ SMPP Session    │        │         ▼ SMPP Session       │
│ ┌────┴─────────┐       │        │  ┌──────┴───────┐            │
│ │Secure Tunnel │       │        │  │Secure Tunnel │            │
│ │    Server    │       │        │  │    Server    │            │
│ └────┬─────────┘       │        │  └──────┬───────┘            │
│      ▲                 │        │         ▲                    │
└──────┼─────────────────┘        └─────────┼────────────────────┘
       └────── Secure Session ──────────────┘
```
**Figure 2-18 ESME-MC SMPP session using a secure tunnel**

SMPP V5.0  SMS Forum 46 of 166

##### 2.11 Forward and Backward Compatibility

SMPP is an evolving protocol and V5.0 represents another version of the protocol. The previous versions that are commonly used are V3.3 and V3.4.

Version 3.4 introduced many new features that enhanced existing PDUs to support TLVs. To preserve backwards compatibility for older V3.3 compliant applications, V3.4 required the *bind* operations to set the *interface_version* to 0x34. V5.0 continues this approach and requires the *interface_version* to be set to 0x50.

##### 2.11.1 Forward Compatibility

Forward Compatibility procedures allow a functional entity (i.e. MC or ESME) using one version of the SMPP protocol to easily communicate with an entity using a later, more enhanced version of the protocol. Hence, new enhancements to existing SMPP PDUs are implemented using TLVs. The following guidelines must be applied in SMPP implementations to ensure that this process is implemented successfully and consistently:

- If a SMPP entity receives an unrecognized PDU/command, it must return a *generic_nack* PDU with the *command_status* field set to ESME_RINVCMDID.
- The SMPP entity receiving a message which includes TLVs shall first inspect the Tag field of the TLV, as follows: o If the TLV Tag is recognized and supported by the receiving SMPP entity for
the particular SMPP operation, the TLV shall be processed. o If a TLV Tag is recognized but not expected for the particular SMPP operation, the TLV shall be ignored. o If the TLV Tag is unrecognized or unsupported by the receiving SMPP entity, that particular TLV shall be ignored and the next TLV in the sequence shall be processed.

- A SMPP entity receiving a parameter value defined as "reserved" should use the default value if a "default" setting is defined, otherwise the parameter should be ignored.
- If the Parameter value is otherwise unrecognized or invalid, the SMPP entity should return an error indicating the Parameter Value is invalid.
- A SMPP entity detecting that a TLV, which is required in the context of the operation, is not present should return the ESME_RMISSINGTLV *command_status*.
- A Variable length field Parameter may have its’ maximum length definition extended in subsequent versions of the SMPP protocol. A SMPP entity receiving a variable length Parameter whose length is greater than the maximum length the entity supports for that Parameter should reject the parameter with its appropriate *command_status* value.
SMPP V5.0  SMS Forum 47 of 166

##### 2.11.2 Backward Compatibility

Backward Compatibility procedures allow a functional entity using one version of the SMPP protocol to communicate with an entity using an older version of the protocol. The following guidelines must be followed in SMPP implementations to ensure that this process is implemented successfully and consistently:

- Existing SMPP PDUs must not be removed from the protocol.
- The effect of receiving any existing message in a new modified format must be same as that in previous versions. Thus the addition of new parameters or parameter values is purely additive.
- TLVs shall not become mandatory parameters. No field formatted as a TLV can be deprecated and replaced by a standard integer, C-Octet string or Octet String field.
- Mandatory parameters shall not become TLVs. No mandatory field can be deprecated and replaced by a TLV. TLVs may be defined to supercede mandatory fields (e.g *message_payload* supercedes *short_message*) but the original mandatory field remains in place and usable by legacy applications.
- In situations where TLVs are defined to supercede a mandatory C-Octet or Octet String, it may become plausable to allow NULL values to be specified for the superceded field, where its’ previous definition disallowed the use of NULL settings.
- Additional mandatory parameters shall not be added to an existing SMPP PDU.
- Existing mandatory parameters shall not be removed from an existing SMPP PDU.
- The meaning of any existing parameter value shall not be changed in the new version of the protocol.
As the concept of TLVs was first introduced in V3.4 of the protocol, the following special guidelines were defined:

- A MC that implements SMPP V3.4 or a later version of this protocol must not send TLVs to an ESME that implements an earlier SMPP version (e.g. V3.3). A MC shall determine the SMPP version supported by an ESME during the bind operation. An ESME supporting SMPP V3.3 or earlier will set the *interface_version* parameter in the bind operation to a value less than 0x34 or equal to 0x00.
- A MC supporting V3.4 or later should return the SMPP version it supports in the *sc_interface_version* parameter of the bind response PDU. If a bind response does not contain the *sc_interface_version* parameter, then the ESME should assume that the MC does not support the use of TLVs.
- An ESME that implements SMPP V3.4 or a later version of this protocol must not send TLVs to a MC that implements an earlier version of this protocol. The ESME shall determine the MC version support from the SMPP bind response PDU.
- A MC that implements SMPP V3.4 or later must not generate message IDs greater than 8 octets when communicating with an ESME that supports SMPP V3.3 or earlier.
SMPP V5.0  SMS Forum 48 of 166

#### 3 SMPP Parameter and PDU Format

This section defines the various types used to build SMPP PDUs. It also describes the layout of a PDU and how it appears when encoded.

##### 3.1 Parameter Type Definitions

All SMPP PDUs comprise of organised sets of parameters. These parameters can have any of the following formats:

**Note:** Values depicted with a 0x prefix are in Hexidecimal format, meaning that each digit

represents 4 binary bits. Thus, a 2-digit hex number is represented by 1 octet of data.

|Parameter Type|Description|
|---|---|
|Integer|An unsigned integer value, which can be 1, 2 or 4 octets in size. The octets are always encoded in Most Significant Byte (MSB) first order, otherwise known as Big Endian Encoding. A 1-octet Integer with a value 5, would be encoded in a single octet with the value 0x05 A 2-octet integer with the decimal value of 41746 would be encoded as 2 octets with the value 0xA312 A 4-octet integer with the decimal value of 31022623 would be encoded as 4 octets with the value 0x1D95E1F|
|C-Octet String|A C-Octet String is a sequence of ASCII characters terminated with a NULL octet (0x00). The string `Hello` would be encoded in 6 octets (5 characters of `Hello` and NULL octet) as follows: 0x48656C6C6F00 Two special variants exist for use within SMPP. These are C-octet String (Decimal) and C-Octet String (Hexadecimal), which are used to carry decimal and hexadecimal digit sequences respectively. These fields are encoded the same way as any ASCII string, but are specifically used to designate decimal and hexadecimal numbers when presented in string format. A Decimal C-Octet String `123456789` would be encoded as follows: 0x31323334353637383900 A Hexadecimal C-Octet String `A2F5ED278FC` would be encoded as follows: 0x413246354544323738464300|

SMPP V5.0  SMS Forum 49 of 166

|Parameter Type|Description|
|---|---|
|Octet String|An Octet String is a sequence of octets not necessarily terminated with a NULL octet. Such fields using Octet String encoding, typically represent fields that can be used to encode raw binary data. In all circumstances, the field will be either a fixed length field or explicit length field where another field indicates the length of the Octet String field. An example of this is the short_message field of the submit_sm PDU that is Octet String encoded and the previous message_length field specifies its length.|
|Tagged Length Value (TLV)|A Tagged Length Value Field is a special composite field that comprises of three parts: • A 2-octet Integer (Tag) The tag identifies the parameter. • A 2-octet Integer (Length) The length field indicates the length of the value field in octets. Note that this length does not include the length of the tag and length fields. • An Octet String (Value) The value field contains the actual data for the TLV field. The Tag identifies the parameter. The Length indicates the size of the Value field in octets. An example of a TLV is the dest_bearer_type. Its Tag is 0x0007 and has a value size of 1 octet. The value 0x04 indicates USSD as a bearer type. In its encoded form, this TLV would appear as follows: 0x0007000104 The first 2 octets 0x0007 identifies the Tag dest_bearer_type. The next two octets 0x0001 indicate the 1-octet length of the value field. The value field 0x04 indicates USSD ref. 4.8.4.64|

**Table 3-1 SMPP PDU Parameter Types**

SMPP V5.0  SMS Forum 50 of 166

##### 3.1.1 NULL Settings

References to a NULL setting for a field imply that the field is not carrying a value. However the NULL octet must still be encoded in the PDU. The following examples indicate how NULL settings are handled for each of the SMPP PDU Parameter Types.

|Parameter Type|NULL Setting Encoding|
|---|---|
|Integer|1-Octet: 0x00 2-Octet: 0x0000 4-Octet: 0x00000000|
|C-Octet String|A NULL string `` is encoded as 0x00|
|Octet String|A NULL Octet-String is not encoded. The explicit length field that indicates its length should be set to zero.|
|Tagged Length Value (TLV)|There are two types of NULL encoding for a TLV. The first is a TLV that may not carry a value part. An example of such a TLV is alert_on_message_delivery. This TLV is typically used as an indicator only, i.e. its function is driven by its very presence in the PDU. No data is typically present. However it may carry up to 1 octet of data if required. Here are two examples of how this TLV can be encoded, the first example carries a value, the second example does not: Tag=0x130C Length=0x0001 Value=0x01 Encoded Format: 0x130C000101 Tag=0x130C Length=0x0000 Value=NULL Encoded Format: 0x130C0000 Note: Only the Tag and Length are encoded. No NULL octets are specified for the zero length Value field. If the TLV itself is not required, then it is not encoded at all. The very absence of the TLV from the PDU is the means by which we set the values to NULL.|

**Table 3-2 SMPP PDU Parameter Type NULL Settings**

SMPP V5.0  SMS Forum 51 of 166

##### 3.1.2 SMPP Parameter Field Size Notation

The following notation style is used throughout. Note that some SMPP strings are optional and others mandatory.

##### Description of String type specified

|Size|Type||
|---|---|---|
|octets|||
|1|Integer|Fixed size integer field. In this example the integer is of size 8 bits (1 octet)|
|2|Integer|Fixed size integer field. In this example the integer is of size 16 bits (2 octets)|
|4|Integer|Fixed size integer field. In this example the integer is of size 32 bits (4 octets)|
|Var|C-Octet|This string is of variable length from 1-15 ASCII characters,|
|Max 16|String|followed by an octet containing the NULL terminator. An empty string is encoded as a single octet containing the NULL character (0x00).|
|Fixed|C-Octet|This string has two possible lengths:|
|1 or 17|String|1 octet containing the NULL character or A fixed number of characters terminated with the NULL character (in this example 16 characters plus the NULL character).|
|Var|Octet|Variable size octet string field. In this example the size of the|
|0 - 255|String|octet string field can vary from 0 to 255 octets.|

**Table 3-3 SMPP PDU Parameter Type Size Notation**

SMPP V5.0  SMS Forum 52 of 166

##### 3.2 General PDU Format

The general format of a SMPP PDU consists of a PDU header followed by a body as outlined in the following:

##### SMPP PDU

##### PDU Header (mandatory) PDU Body (Optional)

Command Command Command Sequence PDU Body length id status number

4 octets 4 octets 4 octets 4 octets Length = (Command Length value - 16) octets

##### 4 octets Command Length - 4

**Table 3-4 SMPP PDU Format**

The 16-0ctet SMPP Header is a mandatory part of every SMPP PDU and must always be present. The SMPP PDU Body is optional and may not be included with every SMPP PDU. There are two ways of viewing a PDU; either as a 16-octet header plus (command_length –

16) octets for the body or as a 4-octet command_length plus (command_length – 4) octets for the remaining PDU data.

##### 3.2.1 PDU Format

|SMPP PDU Field|Size|Type Description||
|---|---|---|---|
||(Octets)|||
|command_length|4|Integer Overall size of PDU including|header and body|
|command_id|4|Integer|Identifies the PDU|
|command_status|4|Integer|Used to carry a SMPP error code|
|PDU HEADER sequence_number|4|Integer|Used to uniquely identify a SMPP PDU in the context of a SMPP session|
|Standard Parameters|var. mixed||The Body part of a PDU differs from PDU to PDU and in some|
|BODY TLV Parameters|var.|mixed|cases, there is no body at all.|

**Table 3-5 SMPP PDU Format**

The layout of every SMPP PDU consists of a mandatory 16 octets representing the PDU header. The above fields are now explained in detail:

##### 3.2.1.1 Command_length

Command_length represents the actual size of the PDU including the PDU header and body. Note that the command_length is included.

The reason for requiring a command_length field is that SMPP is a binary protocol and also supports asynchronous transmission. This means that an ESME or MC must support the ability to decode a PDU from a network connection buffer that may contain several PDUs. The key to achieving the means of decoding each PDU within a buffer is based on the knowledge of how big each PDU is. The *command_length* represents the first field of a PDU and its value contains the overall size of the PDU. An application can easily decode a PDU from a buffer by extracting the first 4 octets, assuming these to represent the

SMPP V5.0  SMS Forum 53 of 166

*command_length* and then deduce from the value, the overall size of the PDU. Considering that 4 octets have been read from the buffer, the deduced value is decremented by 4 to indicate the remaining PDU data to extract from the buffer.

An alternative approach to this is to view each SMPP PDU as a mandatory block of 16 octets representing the PDU header. So an application wishing to decode a PDU for processing must wait until there are at least 16 octets available in the network connection buffer or continually read octets of data until 16 octets have been read. This can then be broken down into the four 4-octet fields that make up the PDU Header. By subtracting 16 from *command_length* value, the application can evaluate the size of the PDU Body and use the same means of reading data from its buffers until the remaining data has been received. Ref.

4.7.4

##### 3.2.1.2 Command_id

The *command_id* identifies the SMPP operation e.g. *submit_sm*, *bind_transmitter* etc. The *command_id* is encoded as a 4-octet integer value.

Command_ids for request PDUs are allocated from a range of numbers; 0x00000000 to 0x000001FF.

Command_ids for response PDUs are allocated from a range of numbers; 0x80000000 to 0x800001FF.

The relationship between the *command_id* for a request PDU and its associated response PDU is that bit 31 is cleared for the request and set for the response. For example, *replace_sm* has a *command_id* = 0x**0**0000007 and its’ response PDU *replace_sm_resp* has a *command_id* = 0x80000007. Ref. 4.7.5

##### 3.2.1.3 Command_status

The *command_status* represents the means by which an ESME or MC sends an error code to its peer. This field is only relevant in response PDUs. Thus PDU requests always have this field set to NULL (0x00000000).

**Note:** When a response PDU carries a non-NULL *command_status* field, it is indicating some

form of error or rejection of the original request PDU. In such circumstances, a PDU body should not be included in the PDU and the *command_length* of the PDU should therefore be set to 16 (0x00000010). However some ESMEs or Message Centers may always include a PDU body regardless of the *command_status* being returned. In such circumstances, the receiving ESME or MC should ignore its contents, based on the knowledge that the original request failed. Ref. 4.7.6

##### 3.2.1.4 Sequence_number

The sequence_number as already described in section 2.6 represents a means of uniquely identifying each PDU within a SMPP session. It also provides a means of correlating request and response PDUs based on matching sequence number. Ref. 4.7.24

##### 3.2.1.5 Standard Parameters

Standard Parameters consist of combinations of Integer, C-Octet Strings and Octet-Strings that form the basic layout of any PDU body. These fields are always present even if specified in NULL form.

##### 3.2.1.6 TLV Parameters

Tagged Length Value (TLV) parameters as described in section 3.1 are identified by a tag, length and value and can be appended to a PDU in any order. The only requirement is that the PDU’s standard fields are first encoded, and then followed by the TLV parameters. Otherwise, the PDU decoding by the peer would be unable to decode the PDU.

TLVs were originally referred to as Optional Parameters in SMPP V3.4. In Version 5.0 of the protocol, the term Optional Parameter applies only to TLVs that are not mandatory within a

SMPP V5.0  SMS Forum 54 of 166

PDU. Therefore TLVs are described in the context of a PDU as Mandatory TLVs and Optional TLVs. The Term Optional Parameter is no longer used.

The reason for the change of terminology is that some new PDUs now feature TLVs as mandatory fields that must always be present. In this context, generally referring to these fields as Optional Parameters, is misleading, hence the change of terminology.

##### 3.2.2 A sample PDU

The following PDU example illustrates how a SMPP PDU is decoded:

Sample PDU (Values are shown in Hex format): 00 00 00 2F 00 00 00 02 00 00 00 00 00 00 00 01 53 4D 50 50 33 54 45 53 54 00 73 65 63 72 65 74 30 38 00 53 55 42 4D 49 54 31 00 50 01 01 00

The 16-octet header would be decoded as follows:

|00 00 00 2F|Command Length|0x0000002F|
|---|---|---|
|00 00 00 02 Command ID 0x00000002 (bind_transmitter)|||
|00 00 00 00|Command Status|0x00000000|
|00 00 00 01|Sequence Number|0x00000001|

The remaining data represents the PDU body (which in this example relates to the *bind_transmitter* PDU).

##### This is diagnosed as follows

|53 4D 50 50 33 54 45 53 54 00|system_id (`SMPP3TEST`)|
|---|---|
|73 65 63 72 65 74 30 38 00|password (`secret08`)|
|53 55 42 4D 49 54 31 00|system_type (`SUBMIT1`)|
|50|interface_version (0x50 `V5.0 compliant`)|
|01|addr_ton (0x01)|
|01|addr_npi (0x01)|
|00|addr_range (NULL)|

SMPP V5.0  SMS Forum 55 of 166

#### 4 SMPP PDU Definitions

This section defines the various Operation PDUs that make up the SMPP protocol. The Operations are described in 6 categories: Session Management, Message Submission, Message Delivery, Message Broadcast, Anciliary Submission and Anciliary Broadcast operations.

##### 4.1 Session Management Operations

These operations are used to establish and maintain a SMPP session.

##### 4.1.1 Bind Operation

The purpose of the SMPP bind operation is to register an instance of an ESME with the MC system and request a SMPP session over this network connection for the submission or delivery of messages. Thus, the Bind operation may be viewed as a form of MC login request to authenticate the ESME entity wishing to establish a connection.

As described previously, an ESME may bind to the MC as a Transmitter (called ESME Transmitter), a Receiver (called ESME Receiver), or a Transceiver (called ESME Transceiver). There are three SMPP bind PDUs to support the various modes of operation, namely *bind_transmitter*, *bind_transceiver* and *bind_receiver*. The *command_id* field setting specifies which PDU is being used.

An ESME may bind as both a SMPP Transmitter and Receiver using separate *bind_transmitter* and *bind_receiver* operations (having first established two separate network connections). Alternatively an ESME can also bind as a Transceiver having first established a single network connection.

##### 4.1.1.1 bind_transmitter Syntax

The format of the SMPP *bind_transmitter* PDU is defined in the following table:

||max 16|String|requesting to bind as a transmitter with the MC.|4.7.30|
|---|---|---|---|---|
|password Var.|max 9|C-Octet String|The password may be used by the MC to authenticate the ESME requesting to bind.|4.7.18|
|system_type Var.|max 13|C-Octet String|Identifies the type of ESME system requesting to bind as a transmitter with the MC.|4.7.31|

##### Field Name Size Type Description Ref

**octets**

command_length 4 Integer Defines the overall length of the 4.7.4 *bind_transmitter* PDU.

##### command_id 4 Integer 0x00000002 4.7.5

##### command_status 4 Integer 0x00000000 4.7.6

sequence_number 4 Integer Set to a unique sequence number. 4.7.24

The associated *bind_transmitter_resp* PDU will echo the same sequence number.

system_id Var. C-Octet Identifies the ESME system

SMPP V5.0  SMS Forum 56 of 166

|Field Name|Size|Type|Description|Ref.|
|---|---|---|---|---|
||octets||||
|interface_version 1 Integer Indicates the version of the SMPP|||protocol supported by the ESME.|4.7.13|
|addr_ton|1|Integer|Indicates Type of Number of the ESME address. If not known set to NULL.|4.7.1|
|addr_npi|1|Integer|Numbering Plan Indicator for ESME address. If not known set to NULL.|4.7.2|
|address_range Var.|max 41|C-Octet String|The ESME address. If not known set to NULL.|4.7.3|

**Table 4-1 *bind_transmitter* PDU**

##### 4.1.1.2 bind_transmitter_resp Syntax

The SMPP *bind_transmitter_resp* PDU is used to reply to a bind_transmitter request. The format of the SMPP *bind_transmitter_resp* PDU is defined in the following table.

|Field Name|Size|Type|Description|Ref.|
|---|---|---|---|---|
||octets||||
|command_length|4|Integer|Defines the overall length of the bind_transmitter_resp PDU.|4.7.4|
|command_id|4|Integer|0x80000002|4.7.5|
|command_status|4|Integer|Indicates status (success or error code) of original bind_transmitter request.|4.7.6|
|sequence_number|4|Integer|Set to sequence number of original bind_transmitter request.|4.7.24|

system_id Var. C-Octet MC identifier. 4.7.30 max 16 String Identifies the MC to the ESME.

##### Optional TLVs

sc_interface_version TLV SMPP version supported by MC 4.8.4.51

**Table 4-2 *bind_transmitter_resp* PDU**

SMPP V5.0  SMS Forum 57 of 166

##### 4.1.1.3 bind_receiver Syntax

The format of the SMPP *bind_receiver* PDU is defined in the following table.

|Field Name|Size|Type|Description|Ref.|
|---|---|---|---|---|
||octets||||
|command_length|4|Integer|Defines the overall length of the PDU in octets.|4.7.4|
|command_id|4|Integer|0x00000001|4.7.5|
|command_status|4|Integer|0x00000000|4.7.6|
|sequence_number|4|Integer|Set to a unique sequence number. The associated bind_receiver_resp PDU will echo the same sequence number.|4.7.24|

|System_id Var.||C-Octet|Identifies the ESME system|4.7.30|
|---|---|---|---|---|
||max 16|String|requesting to bind as a receiver with the MC.||
|password Var.|max 9|C-Octet String|The password may be used by the MC for security reasons to authenticate the ESME requesting to|4.7.18|
||||bind.||
|system_type Var. interface_version 1 Integer Identifies the version of the SMPP|max 13|C-Octet String|Identifies the type of ESME system requesting to bind as a receiver with the MC. protocol supported by the ESME.|4.7.31 4.7.13|
|addr_ton|1|Integer|Type of Number (TON) for ESME address(es) served via this SMPP receiver session. Set to NULL if not known.|4.7.1|
|addr_npi|1|Integer|Numbering Plan Indicator (NPI) for ESME address(es) served via this SMPP receiver session. Set to NULL if not known.|4.7.2|
|address_range Var.|max 41|C-Octet String|A single ESME address or a range of ESME addresses served via this|4.7.3|

##### SMPP receiver session

##### Set to NULL if not known

**Table 4-3 *bind_receiver* PDU**

SMPP V5.0  SMS Forum 58 of 166

##### 4.1.1.4 bind_receiver_resp Syntax

The format of the SMPP *bind_receiver_resp* PDU is defined in the following table*.*

|Field Name|Size|Type|Description|Ref.|
|---|---|---|---|---|
||Octets||||
|command_length|4||Integer Defines the overall length of the|4.7.4|
||||PDU.||
|command_id|4||Integer 0x80000001|4.7.5|
|command_status|4||Integer Indicates status (success or error code) of original bind_receiver request.|4.7.6|
|sequence_number|4||Integer Set to sequence number of original bind_receiver request.|4.7.24|

System_id Var. C-MC identifier. 4.7.30 max 16 Octet Identifies the MC to the ESME. String

##### Optional TLVs

##### TLV Name Type Description

sc_interface_version TLV SMPP version supported by MC 4.8.4.51

**Table 4-4 *bind_receiver_resp* PDU**

##### 4.1.1.5 bind_transceiver Syntax

The format of the SMPP *bind_transceiver* PDU is defined in the following table***.***

|Field Name|Size Octets|Type|Description|Ref.|
|---|---|---|---|---|
|command_length|4|Integer|Defines the overall length of the PDU.|4.7.4|
|command_id|4|Integer|0x00000009|4.7.5|
|command_status|4|Integer|0x00000000|4.7.6|
|sequence_number|4|Integer|Set to a unique sequence number. The associated bind_transceiver_resp PDU will echo the same sequence number.|4.7.24|

|system_id Var.||C-Octet|Identifies the ESME system|4.7.30|
|---|---|---|---|---|
||max 16|String|requesting to bind as a transceiver with the MC.||
|password Var.|max 9|C-Octet String|The password may be used by the MC to authenticate the ESME requesting to bind.|4.7.18|
|system_type Var.|max 13|C-Octet String|Identifies the type of ESME system requesting to bind as a transceiver with the MC.|4.7.31|
|SMPP V5.0||| SMS Forum|59 of 166|

##### Field Name Size Type Description Ref

##### Octets

interface_version 1 Integer Identifies the version of the SMPP 4.7.13 protocol supported by the ESME.

addr_ton 1 Integer Type of Number (TON) for ESME 4.7.1 address(es) served via this SMPP transceiver session.

Set to NULL (Unknown) if not known.

addr_npi 1 Integer Numbering Plan Indicator (NPI) for 4.7.2 ESME address(es) served via this SMPP transceiver session.

Set to NULL (Unknown) if not known.

address_range Var. C-Octet A single ESME address or a range of 4.7.3 max 41 String ESME addresses served via this SMPP transceiver session.

##### Set to NULL if not known

**Table 4-5 *bind_transceiver* PDU**

##### 4.1.1.6 bind_transceiver_resp Syntax

The format of the SMPP *bind_transceiver_resp* PDU is defined in the following table*.*

|Field Name|Size|Type|Description|Ref.|
|---|---|---|---|---|
||Octets||||
|command_length|4||Integer Defines the overall length of the PDU.|4.7.4|
|command_id|4||Integer 0x80000009|4.7.5|
|command_status|4||Integer Indicates status (success or error code) of original bind_transceiver request.|4.7.6|
|sequence_number|4||Integer Set to sequence number of original bind_transceiver request.|4.7.24|

system_id Var. C-MC identifier. 4.7.30 max 16 Octet Identifies the MC to the ESME. String

##### Optional TLVs

##### TLV Name Type Description

sc_interface_version TLV SMPP version supported by MC 4.8.4.51

**Table 4-6 *bind_transceiver_resp* PDU**

SMPP V5.0  SMS Forum 60 of 166

##### 4.1.1.7 outbind Syntax

This operation is used by the MC to signal an ESME to originate a *outbind* request to the MC. The format of the SMPP *outbind* PDU is defined in the following table.

|Field Name|Size Octets|Type|Description|Ref.|
|---|---|---|---|---|
|command_length|4|Integer|Defines the overall length of the|4.7.4|
||||PDU.||
|command_id|4|Integer|0x0000000B|4.7.5|
|command_status|4|Integer|0x00000000|4.7.6|
|sequence_number|4|Integer|Set to a unique sequence number.|4.7.24|

|system_id Var.||C-Octet|MC identifier.|4.7.30|
|---|---|---|---|---|
||max|String|Identifies the MC to the ESME.||
||16||||
|password Var.|max 9|C-Octet String|The password may be used by the ESME for security reasons to|4.7.18|

authenticate the MC originating the outbind.

**Table 4-7*outbind PDU***

##### 4.1.1.8 unbind Syntax

The purpose of the SMPP *unbind* operation is to deregister an instance of an ESME from the MC and inform the MC that the ESME no longer wishes to use this network connection for the submission or delivery of messages.

Thus, the *unbind* operation may be viewed as a form of MC logoff request to close the current SMPP session. The format of the SMPP *unbind* PDU is defined in the following table:

##### Description

|Field Name|Size|Type||Ref.|
|---|---|---|---|---|
||octets||||
|command_length|4|Integer|Defines the overall length of the PDU.|4.7.4|
|command_id|4|Integer|0x00000006|4.7.5|
|command_status|4|Integer|0x00000000|4.7.6|
|sequence_number|4|Integer|Set to a unique sequence number.|4.7.24|

The associated *unbind_resp* PDU will echo the same sequence number.

**Table 4-8 *unbind* PDU**

SMPP V5.0  SMS Forum 61 of 166

##### 4.1.1.9 unbind_resp Syntax

The SMPP *unbind_resp* PDU is used to reply to an *unbind* request. It comprises the SMPP message header only*.*

The format of the SMPP *unbind_resp* PDU is defined in the following table:

|Field Name|Size octets|Type|Description|Ref.|
|---|---|---|---|---|
|command_length|4|Integer|Defines the overall length of the PDU.|4.7.4|
|command_id|4|Integer|0x80000006|4.7.5|
|command_status|4|Integer|Indicates outcome of original unbind request.|4.7.6|
|sequence_number|4|Integer|Set to sequence number of original unbind request.|4.7.24|

**Table 4-9 *unbind_resp* PDU**

SMPP V5.0  SMS Forum 62 of 166

##### 4.1.2 Enquire Link Operation

This PDU can be originated by either the ESME or MC and is used to provide a confidence- check of the communication path between an ESME and a MC. On receipt of this request the receiving party should respond with an *enquire_link_resp*, thus verifying that the application level connection between the MC and the ESME is functioning. The ESME may also respond by sending any valid SMPP primitive.

##### 4.1.2.1 enquire_link Syntax

|Field Name|Size|Type|Description|Ref.|
|---|---|---|---|---|
||octets||||
|command_length|4|Integer|Set to overall length of PDU|4.7.4|
|command_id|4|Integer|0x00000015|4.7.5|
|command_status|4|Integer|0x00000000|4.7.6|
|sequence_number|4|Integer|Set to a unique sequence number. The associated enquire_link_resp PDU should echo the same sequence number|4.7.24|

**Table 4-10 *enquire_link* PDU**

##### 4.1.2.2 enquire_link_resp Syntax

The *enquire_link_resp* PDU is used to reply to an *enquire_link* request.

|Field Name|Size|Type|Description|Ref.|
|---|---|---|---|---|
||octets||||
|command_length|4|Integer|Set to overall length of PDU|4.7.4|
|command_id|4|Integer|0x80000015|4.7.5|
|command_status|4|Integer|0x00000000|4.7.6|
|sequence_number|4|Integer|Set to the same sequence number of original enquire_link PDU|4.7.24|

**Table 4-11 *enquire_link_resp* PDU**

SMPP V5.0  SMS Forum 63 of 166

##### 4.1.3 Alert Notification Operation

The *alert_notification* PDU is sent by the MC to the ESME across a Receiver or Transceiver session. It is sent when the MC has detected that a particular mobile subscriber has become available and a delivery pending flag had been previously set for that subscriber by means of the *set_dpf* TLV (ref. 4.8.4.52).

A typical use of this operation is to trigger a data content ‘Push’ to the subscriber from a WAP Proxy Server.

Note: There is no associated *alert_notification_resp* PDU.

##### 4.1.3.1 alert_notification Syntax

Following is the format of the SMPP *alert_notification* PDU.

|Field Name|Size octets|Type|Description|Ref.|
|---|---|---|---|---|
|command_length|4|Integer|Defines the overall length of the PDU.|4.7.4|
|command_id|4|Integer|0x00000102|4.7.5|
|command_status|4|Integer|0x00000000|4.7.6|
|sequence_number|4|Integer|Set to a unique sequence number.|4.7.24|

##### Number for alert SME

|source_addr_ton 1 Integer Type of||||4.7.1|
|---|---|---|---|---|
|source_addr_npi|1|Integer|Numbering Plan Indicator for alert|4.7.2|
||||SME.||
|source_addr Var.|max|C-Octet Address of alert SME. String||4.7.29|
||65||||
|esme_addr_ton|1|Integer|Type of Number for ESME address which requested the alert|4.7.1|
|esme_addr_npi|1|Integer|Numbering Plan Indicator for ESME address which requested the alert|4.7.2|
|esme_addr Var.|max|C-Octet Address for ESME which requested String|the alert|4.7.11|
||65||||
|Optional TLVs:|||||
|TLV Name||Type|Description||

ms_availability_status TLV The status of the mobile station

4.8.4.39
**Table 4-12 *alert_notification* PDU**

SMPP V5.0  SMS Forum 64 of 166

##### 4.1.4 Generic NACK Operation

The *generic_nack* PDU is used to acknowledge the submission of an unrecognized or corrupt PDU. The scenarios for returning this PDU are explained in detail in section 2.8.2

##### 4.1.4.1 generic_nack Syntax

Following is the format of the SMPP *generic_nack* PDU***.*** It comprises the SMPP message header only***.***

|Field Name|Size octets|Type|Description|Ref.|
|---|---|---|---|---|
|command_length|4|Integer|Defines the overall length of the PDU.|4.7.4|
|command_id|4|Integer|0x80000000|4.7.5|
|command_status|4|Integer|Error code corresponding to reason for sending the generic_nack.|4.7.6|
|sequence_number|4|Integer|Set to sequence number of original PDU or to NULL if the original PDU cannot be decoded.|4.7.24|

**Table 4-13 *generic_nack* PDU**

SMPP V5.0  SMS Forum 65 of 166

##### 4.2 Message Submission Operations

Message submission operations provide an ESME with the ability to submit messages for onward delivery to mobile stations.

##### 4.2.1 submit_sm Operation

This operation is used by an ESME to submit a short message to the MC for onward transmission to a specified short message entity (SME).

##### 4.2.1.1 submit_sm Syntax

|Field Name|Size|Type|Description|Ref.|
|---|---|---|---|---|
||octets||||
|command_length|4|Integer|Set to overall length of PDU.|4.7.4|
|command_id|4|Integer|0x00000004|4.7.5|
|command_status|4|Integer|0x00000000|4.7.6|
|sequence_number|4|Integer|Set to a Unique sequence number. The associated submit_sm_resp PDU will echo this sequence number.|4.7.24|

service_type C-Octet The *service_type* 4.7.25 String parameter can be used to Var. indicate the SMS max 6 Application service associated with the message. Specifying the *service_type* allows the ESME to avail of enhanced messaging services such as `replace by *service_type*` or to control the teleservice used on the air interface.

Set to NULL for default MC settings

source_addr_ton 1 Integer Type of Number for source 4.7.1 address.

If not known, set to NULL (Unknown).

source_addr_npi 1 Integer Numbering Plan Indicator 4.7.2 for source address.

If not known, set to NULL (Unknown).

source_addr Var. C-Octet Address of SME which 4.7.29 max 21 String originated this message.

If not known, set to NULL (Unknown).

dest_addr_ton Integer Type of Number for 4.7.1 destination

SMPP V5.0  SMS Forum 66 of 166

|Field Name|Size octets|Type|Description|Ref.|
|---|---|---|---|---|
|dest_addr_npi|1|Integer|Numbering Plan Indicator for destination|4.7.2|
|destination_addr Var.|max 21|C-Octet String|Destination address of this short message For mobile terminated messages, this is the directory number of the recipient MS|4.7.8|
|esm_class|1 protocol_id 1 Integer Protocol Identifier.|Integer|Indicates Message Mode and Message Type Network specific field.|4.7.12 4.7.20|
|priority_flag|1|Integer|Designates the priority level of the message|4.7.19|
|schedule_delivery_time|1 or 17|C-Octet String|The short message is to be scheduled by the MC for delivery. Set to NULL for immediate message delivery|4.7.23.1|
|validity_period|1 or 17|C-Octet String|The validity period of this message. Set to NULL to request the MC default validity period Note: this is superseded by the qos_time_to_live TLV if specified. Ref. 4.8.4.46|4.7.23.2|
|registered_delivery|1|Integer|Indicator to signify if a MC delivery receipt, manual ACK, delivery ACK or an intermediate notification is required.|4.7.21|
|replace_if_present_flag 1 Integer|||Flag indicating if the submitted message should replace an existing message.|4.7.22|
|data_coding|1|Integer|Defines the encoding scheme of the short message user data.|4.7.7|
|sm_default_msg_id|1|Integer|Indicates the short message to send from a list of pre- defined (‘canned’) short messages stored on the MC. If not using a MC canned message, set to NULL.|4.7.27|
|sm_length|1|Integer|Length in octets of the short_message user data.|4.7.28|

SMPP V5.0  SMS Forum 67 of 166

|Field Name||Size octets||Type||Description||Ref.|
|---|---|---|---|---|---|---|---|---|
|short_message Var.||0-255||Octet String||Up to 255 octets of short message user data. The exact physical limit for short_message size may vary according to the underlying network Note: this field is superceded by the message_payload TLV if specified. Ref. 4.8.4.36||4.7.26|
|Message Submission TLVs|||Var. TLV|||||4.2.4|
|4.2.1.2||max 65|Table 4-14|submit_sm String||PDU message ID of the submitted message. It may be used at a later stage to query the status of a message, cancel or replace the message.||4.7.14|
|Message Submission Response TLVs||Var. TLV||||||4.2.5|

***submit_sm_resp* Syntax**

##### Field Name Size Type Description Ref

**octets**

command_length 4 Integer Set to overall length of PDU. 4.7.4

##### command_id 4 Integer 0x80000004 4.7.5

command_status 4 Integer Indicates outcome of 4.7.6 *submit_sm* request.

sequence_number 4 Integer Set to sequence number of 4.7.24 original *submit_sm* PDU.

message_id Var. C-Octet This field contains the MC

**Table 4-15 *submit_sm_resp* PDU**

SMPP V5.0  SMS Forum 68 of 166

##### 4.2.2 data_sm Operation

The *data_sm* operation is similar to the *submit_sm* in that it provides a means to submit a mobile-terminated message. However, *data_sm* is intended for packet-based applications such as WAP in that it features a reduced PDU body containing fields relevant to WAP or packet-based applications.

##### 4.2.2.1 data_sm Syntax

|Field Name|Size octets|Type|Description|Ref.|
|---|---|---|---|---|
|command_length|4|Integer|Set to overall length of PDU.|4.7.4|
|command_id|4|Integer|0x00000103|4.7.5|
|command_status|4|Integer|0x00000000|4.7.6|
|sequence_number|4|Integer|Set to a Unique sequence number. The associated data_sm_resp PDU will echo this sequence number.|4.7.24|

service_type C-Octet The *service_type* parameter 4.7.25 String can be used to indicate the Var. SMS Application service max 6 associated with the message. Specifying the *service_type* allows the ESME to avail of enhanced messaging services such as `replace by *service_type*` or control the teleservice used on the air interface.

Set to NULL for default MC settings

|source_addr_ton|1|Integer|Type of Number for source|4.7.1|
|---|---|---|---|---|
||||address. If not known, set to NULL (Unknown).||
|source_addr_npi|1|Integer|Numbering Plan Indicator for source address. If not known, set to NULL (Unknown).|4.7.2|
|source_addr Var.|max 65|C-Octet String|Address of SME which originated this message. If not known, set to NULL (Unknown).|4.7.29|
|dest_addr_ton|1|Integer|Type of Number for destination|4.7.1|
|dest_addr_npi|1|Integer|Numbering Plan Indicator for destination|4.7.2|
|SMPP V5.0|| SMS Forum||69 of 166|

##### Field Name Description

||Size|Type||Ref.|
|---|---|---|---|---|
||octets||||
|destination_addr Var.|max 65|C-Octet String|Destination address of this short message For mobile terminated messages, this is the directory number of the recipient MS.|4.7.8|
|esm_class|1|Integer|Indicates Message Mode and Message Type|4.7.12|
|registered_delivery|1|Integer|Indicator to signify if a MC delivery receipt or an SME acknowledgement is required.|4.7.21|
|data_coding|1|Integer|Defines the encoding scheme of the short message user|4.7.7|
||||data.||
|Message Submission|Var. TLV|||4.2.4|

TLVs

**Table 4-16 *data_sm* PDU**

##### 4.2.2.2 data_sm_resp Syntax

|Field Name|Size|Type|Description|Ref.|
|---|---|---|---|---|
||octets||||
|command_length|4|Integer|Set to overall length of PDU.|4.7.4|
|command_id|4|Integer|0x80000103|4.7.5|
|command_status|4|Integer|Indicates outcome of data_sm request.|4.7.6|
|sequence_number|4|Integer|Set to sequence number of original data_sm PDU.|4.7.24|

|message_id Var.||C-Octet|This field contains the MC|4.7.14|
|---|---|---|---|---|
||max 65|String|message ID of the submitted message. It may be used at a later stage to query the status of a message, cancel or replace the message.||
|Message Submission|Var. TLV|||4.2.5|

##### Response TLVs

**Table 4-17 *data_sm_resp* PDU**

SMPP V5.0  SMS Forum 70 of 166

##### 4.2.3 submit_multi Operation

The *submit_multi* operation is an enhanced variation of *submit_sm* designed to support up to 255 different destinations instead of the default single destination. It provides an efficient means of sending the same message to several different subscribers at the same time.

##### 4.2.3.1 submit_multi Syntax

|Field Name|Size|Type|Description|Ref.|
|---|---|---|---|---|
||octets||||
|command_length|4||Integer Set to overall length of PDU.|4.7.4|
|command_id|4||Integer 0x00000021|4.7.5|
|command_status|4||Integer 0x00000000|4.7.6|
|sequence_number|4||Integer Set to a Unique sequence number. The associated submit_multi_resp PDU will echo this sequence number.|4.7.24|

service_type C-Octet The service_type parameter 4.7.25 String can be used to indicate the Var. SMS Application service max 6 associated with the message. Specifying the *service_type* allows the ESME to avail of enhanced messaging services such as `replace by *service_type*` or control the teleservice used on the air interface.

Set to NULL for default MC settings

source_addr_ton 1 Integer Type of Number for source 4.7.1 address.

If not known, set to NULL (Unknown).

source_addr_npi 1 Integer Numbering Plan Indicator for 4.7.2 source address.

If not known, set to NULL (Unknown).

source_addr Var. C-Octet Address of SME which 4.7.29 max 21 String originated this message.

If not known, set to NULL (Unknown).

SMPP V5.0  SMS Forum 71 of 166

||Field Name|Size octets|Type|Description|Ref.|
|---|---|---|---|---|---|
||number_of_dests|1||Integer Number of destination addresses – indicates the number of destinations that are to follow. A maximum of 255 destination addresses are allowed. Note: Set to 1 when submitting to one SME Address or when submitting to one Distribution List.|4.7.17|
|dest_address¹|Var. dest_flag dest_addr_ton dest_addr_npi destination_addr Var.|max 24 1 1 1 max 21|C-Octet String|SME Format Destination Address (Composite field) Integer 0x01 (SME Address) Integer Type of Number for destination Integer Numbering Plan Indicator for destination Destination address of this short message. For mobile terminated messages, this is the directory number of the recipient MS|4.7.9 4.7.1 4.7.2 4.7.8|
|dest_address|1|Var. max 23||Distribution List Format Destination Address (Composite Field)||
|dest_flag||||1 Integer 0x02 (Distribution List)|4.7.9|
|dl_name||Var. max 21|C-Octet String|Name of Distribution List|4.7.10|
|esm_class|schedule_delivery_time|1 protocol_id 1 Integer Protocol Identifier. 1 or 17|priority_flag 1 Integer Designates the priority level of C-Octet String|Integer Indicates Message Mode and Message Type Network specific field. the message The short message is to be scheduled by the MC for delivery. Set to NULL for immediate message delivery|4.7.12 4.7.20 4.7.19 4.7.23.1|

1 This field is a composite field containing a mandatory *dest_flag* field and then either an SME address (*dest_ton*, *dest_npi* & *destination_addr*) or a Distribution List (*dl_name*). Additionally the field can be encoded multiple times according to the value specified in the *number_of_dests* field.

SMPP V5.0  SMS Forum 72 of 166

|Field Name|Size octets|Type|Description|Ref.|
|---|---|---|---|---|
|validity_period|1 or 17|C-Octet String|The validity period of this message. Set to NULL to request the MC default validity period Note: this is superseded by the qos_time_to_live TLV if specified. Ref. 4.8.4.46|4.7.23.2|
|registered_delivery|1 replace_if_present_flag 1 Integer Flag indicating if submitted||Integer Indicator to signify if a MC delivery receipt or an SME acknowledgement is required. message should replace an existing message.|4.7.21 4.7.22|
|data_coding|1||Integer Defines the encoding scheme of the short message user data.|4.7.7|
|sm_default_msg_id|1||Integer Indicates the short message to send from a list of pre- defined (‘canned’) short messages stored on the MC. If not using a MC canned message, set to NULL.|4.7.27|
|sm_length|1||Integer Length in octets of the short_message user data.|4.7.28|
|short_message Var.|0-255|Octet String|Up to 255 octets of short message user data. The exact physical limit for short_message size may vary according to the underlying network Note: this field is superceded by the message_payload TLV if specified. Ref. 4.8.4.36 Applications which need to send messages longer than 255 octets should use the message_payload TLV. In this case the sm_length field should be set to zero|4.7.26|
|Message Submission TLVs|Var. TLV|||4.2.4|

**Table 4-18 *submit_multi* PDU**

SMPP V5.0  SMS Forum 73 of 166

##### 4.2.3.2 submit_multi_resp Syntax

|||max 65|String|message ID of the submitted message. It may be used at a later stage to query the status of a message, cancel or replace the message.|4.7.14|
|---|---|---|---|---|---|
||no_unsuccess unsuccess_sme² Var. dest_addr_ton dest_addr_npi destination_addr Var. C-Octet error_status_code|1 max 27 1 1 max 21 4|String Integer|Integer The number of messages to 4.7.16 destination SME addresses that were unsuccessfully submitted to the MC. This is followed by the specified number of unsuccessful SMEs, each specified in a unsuccess_sme field. Unsucessful SME (Composite Field) Integer Type of number for destination Integer Numbering Plan Indicator for4.7.2 SME Destination Address of SME Indicates the success or failure 4.7.6 of the submit_multi request to this SME address|4.7.1 4.7.8|
||Message Submission Response TLVs|Var. TLV Table 4-19|submit_multi_resp|PDU|4.2.5|

##### Field Name Size Type Description Ref

**octets**

command_length 4 Integer Set to overall length of PDU. 4.7.4

##### command_id 4 Integer 0x80000021 4.7.5

command_status 4 Integer Indicates outcome of 4.7.6 *submit_multi* request.

sequence_number 4 Integer Set to sequence number of 4.7.24 original *submit_multi* PDU.

message_id Var. C-Octet This field contains the MC

2 This field is a composite field containing an SME address (*dest_addr_ton*, *dest_addr_npi* & *destination_addr*) and an error code (*error_status_code*). Additionally the field can be encoded multiple times according to the value specified in the *no_unsuccess* field.

SMPP V5.0  SMS Forum 74 of 166

### SMSFORUM

##### 4.2.4 Message Submission Request TLVs

This section lists TLVs that may be used for message submission operations.

<www.smsforum.net>

##### TLV Name

*alert_on_msg_delivery*

*billing_identification*

*callback_num*

*callback_num_atag*

*callback_num_pres_ind*

##### Description Ref

Request an MS alert signal be invoked on message 4.8.4.2 delivery.

Billing information passed from ESME to MC 4.8.4.3

A call-back number associated with the short message. 4.8.4.15

This parameter can be included a number of times for multiple call-back addresses.

Associates a displayable alphanumeric tag with the call-4.8.4.16 back number.

If this parameter is present and there are multiple instances of the *callback_num* parameter then this parameter must occur an equal number of instances and the order of occurrence determines the particular *callback_num_atag* which corresponds to a particular *callback_num*.

Defines the call-back number presentation and screening. 4.8.4.17

If this parameter is present and there are multiple instances of the *callback_num* parameter then this parameter must occur an equal number of instances and the order of occurrence determines the particular *callback_num_pres_ind* which corresponds to a particular *callback_num*.

|dest_addr_np_country|E.164 information to the operator country code|4.8.4.20|
|---|---|---|
|dest_addr_np_information Number portability information for the destination address 4.8.4.21|||
|dest_addr_np_resolution dest_addr_subunit dest_bearer_type|Number portability query indicator The subcomponent in the destination device for which the 4.8.4.23 user data is intended. The correct bearer type for delivering the user data to the 4.8.4.24 destination|4.8.4.22|
|dest_network_id|Identification of destination network|4.8.4.25|
|dest_network_type|The correct network for the destination device|4.8.4.26|
|dest_node_id|Identification of destination node|4.8.4.27|
|dest_subaddress|The sub-address of the message destination.|4.8.4.28|
|dest_telematics_id|The telematics identifier associated with the destination|4.8.4.29|
|dest_port|Indicates the application port number associated with the destination address of the message. This parameter should be present for WAP applications.|4.8.4.30|
|display_time its_reply_type|Provides the receiving MS with a display time associated with the message. The MS user’s reply method to an SMS delivery message 4.8.4.33 received from the network, is indicated and controlled by this parameter.|4.8.4.31|
|its_session_info SMPP V5.0|Session control information for Interactive Teleservice.|4.8.4.34 75 of 166|

 SMS Forum

|TLV Name|Description|Ref.|
|---|---|---|
|language_indicator|Indicates the language of an alphanumeric text message. 4.8.4.35||
|message_payload|Contains the extended short message user data. Up to|4.8.4.36|

##### 64K octets can be transmitted

Note: The short message data should be inserted in either

the *short_message* or *message_payload* fields. Both fields should not be used simultaneously.

The *sm_length* field should be set to zero if using the *message_payload* parameter.

Note: In the case of *data_sm*, the *message_payload* TLV

is the only means of specifying text.

|more_messages_to_send Indicates that there are more messages to follow for the||4.8.4.38|
|---|---|---|
||destination SME.||
|ms_msg_wait_facilities|This parameter controls the indication and specifies the message type (of the message associated with the MWI) at the mobile station.|4.8.4.40|
|ms_validity|Indicates validity information for this message to the recipient MS.|4.8.4.41|
|number_of_messages|Indicates the number of messages stored in a mail box|4.8.4.43|
|payload_type|defines the type of payload (e.g. WDP, WCMP, etc.).|4.8.4.44|
|privacy_indicator|Indicates the level of privacy associated with the message.|4.8.4.45|
|qos_time_to_live|Time to live as a relative time in seconds from submission. 4.8.4.46||
|sar_msg_ref_num|The reference number for a particular concatenated short 4.8.4.48 message.||
|sar_segment_seqnum|Indicates the sequence number of a particular short message fragment within the concatenated short message.|4.8.4.49|
|sar_total_segments|Indicates the total number of short message segments within the concatenated short message.|4.8.4.50|
|set_dpf|Indicator for setting Delivery Pending Flag on delivery failure.|4.8.4.52|
|sms_signal|Indicates the alerting mechanism when the message is received by an MS.|4.8.4.53|
|source_addr_subunit|The subcomponent in the destination device, which created the user data.|4.8.4.54|
|source_bearer_type|The correct bearer type for delivering the user data to the 4.8.4.55 destination||
|source_network_id|Identification of source network|4.8.4.56|
|source_network_type|The correct network associated with the originating device.|4.8.4.57|
|source_node_id|Identification of source node|4.8.4.58|
|source_port|Indicates the application port number associated with the source address of the message. This parameter should be present for WAP applications.|4.8.4.59|
|source_subaddress|The sub-address of the message originator.|4.8.4.60|
|SMPP V5.0||76 of 166|

#####  SMS Forum

|TLV Name|Description|Ref.|
|---|---|---|
|source_telematics_id user_message_reference ESME assigned message reference number.|The telematics identifier associated with the source.|4.8.4.61 4.8.4.62|
|user_response_code|A user response code. The actual response codes are implementation specific.|4.8.4.63|
|ussd_service_op|This parameter is used to identify the required USSD|4.8.4.64|

Service type when interfacing to a USSD system.

**Table 4-20 Message Submission Request TLVs**

##### 4.2.5 Message Submission Response TLVs

The following table contains TLVs that can be returned in a *submit_sm_res*p or *data_sm_resp* PDU. All TLVs are relevant to transaction message mode only (ref. 4.2.10.4)

|TLV Name|Description|Ref.|
|---|---|---|
|additional_status_info_text|ASCII text giving a description of the meaning of the4.8.4.1 response.||
|delivery_failure_reason dpf_result|Include to indicate reason for delivery failure. Indicates whether the Delivery Pending Flag was set. 4.8.4.32|4.8.4.19|
|network_error_code|Error code specific to a wireless network.|4.8.4.42|

**Table 4-21 Message Submision Response TLVs**

##### 4.2.6 Source and Destination Addressing

The *submit_sm* and *data_*sm PDUs include provision for both ‘source address’ (message sender) and ‘destination address’ (message recipient). The common concept of a source or destination address is a sequence of digits representing a mobile or fixed-line number. However the reality is more complex. Both addresses comprise of three parts, namely the TON (Type of Number), NPI (Numbering Plan Indicator) and Address (Digit sequence). Every time a message is sent either to or from a mobile, the source and destination addresses comprise of the three parts. This three-part relationship is visible in the PDUs in the form of *source_addr_ton*, *source_addr_npi*, *source_addr*, *dest_addr_ton*, *dest_addr_npi* and *destination_addr* fields.

**Note**: Setting the source_addr field to NULL, will force the MC to default the address to some

predetermined value. This is useful for interfaces that are not normally familiar with the notion of a source address for a short message, e.g. one-way paging systems, voice mail systems.

##### 4.2.6.1 TON

##### 4.2.6.1.1 International and National Format

In daily life, people talk about national and international numbers they use for dialing. Such numbers contain an international trunk prefix or the '+' sign, and for many countries a national trunk prefix. In most European countries the '00' indicates the international trunk prefix whereas the '0' is in many countries the national trunk prefix. In the telecommunications world where numbers are digitally transmitted through multiple networks, the Type of Number (TON) values of National and International are an easy means of indicating mobile numbers without dialing prefixes (international trunk prefix, national trunk prefix). The value Unknown is used when number analysis is to be performed for destination routing purposes.

An international TON means that the number starts with the country code followed by the national destination code and the subscriber number. The national destination code is also known as the operator code, operator prefix, and is similar to the fixed-line network area codes. It is common for numbering plans to have multiple national destination codes. In

SMPP V5.0  SMS Forum 77 of 166

some countries, the wireless and fixed-line network numbering plans are fully integrated. On the mobile terminal the '+' is used as a visible cue to indicate an international number. The '+' itself is not encoded in the address, it simply translates to TON International.

Example: a mobile sending a message to +4467811223344 is in fact using a

##### TON=1 and dest_addr = 4467811223344

A national TON means that the number starts with the national destination code followed by the subscriber number. Effectively, it is the international number format with the country code stripped off. When a mobile sends a message, unlike the '+' for international number format, there is no visual cue to indicate a national number. It is purely an address encoding value.

Message Centers typically operate with international numbers as the mobile network interface usually requires this. So ESMEs sending message to the MS should expect that a national number will be expanded into international format. ESMEs should expect the source_addr TON of a mobile originated message to be set to international or national, and that the TON can vary from one message to another, even if they are from the same mobile device. This is especially important for application-based database lookups using the mobile number.

##### 4.2.6.1.2 Alphanumeric Format

Alphanumeric addressing provides a means of using human-readable names for addresses. In SMPP, an alphanumeric address can carry any digit 0-9 and alphabetical character a-z or A-Z. For example a voice mail server may send `Voicemail` as a alphanumeric source address and as a means of indicating this, it will set the TON=5. Some mobiles are capable of sending alphanumeric numbers and accomplish this by means of a TON value of 5. NPI is usually set to 0, when TON is 5.

##### 4.2.6.2 NPI

NPI is generally set to 1 by mobile devices. Its purpose is to specify the numbering plan of the target device, but because these generally tend to be mobiles, the value is generally set to 1.

##### 4.2.6.3 ESME Addresses

Specifying addresses for ESMEs is very much prone to the network operator’s preferences and the message center implementation. An ESME will typically use one of the following approaches:

- Service Short Code Short codes are brief and often easy to remember. Operators providing services that allow a subscriber avail of stock quotes, lotto results etc, will often configure the service to respond to a short digit sequence such as `1234`. The subscriber will mobile originate a message addressed to `1234` (TON=0 `unknown`) and the message center routing will deliver this to an ESME receiver or transceiver. In responding to the message, the ESME will usually set the source address to the short code, making it easy for the recipient to reply to the message if another request is desired.
- International Number With growth in the numbers of subscribers roaming internationally with their mobiles, ESME services that they use, need to be accessible regardless of location. One approach to supporting this accessibility is for the ESME to use an international number in the source address of the message (TON=1, NPI=1).
- NULL Address An ESME Transmitter may enter NULL values in the ‘source address’ fields. In this event, the MC may then substitute a default address for that particular ESME. This feature is designed for interfaces that are not normally familiar with the notion of a source address for a short message, e.g., paging systems, voice mail systems,
SMPP V5.0  SMS Forum 78 of 166

which were developed before the advent of SMS technologies and were based on one-way paging, hence the lack of a source address.

##### 4.2.7 Message Replace operation in submit_sm

Though SMPP offers a dedicated *replace_sm* operation, the *submit_sm* operation also facilitates replacement of a short message which has been previously submitted but has not yet been delivered to the designated destination.

This feature is designed for applications needing the ability to update an undelivered message. A common application of this feature is with a voicemail system. The first message may read `You have 1 message in your mail box`. If the mobile is unavailable, the message will remain undelivered and in retry within the message center. If another message is left for the same subscriber, the voicemail server will send another message with the updated text `You have 2 messages in your mail box`. If the *replace_flag* of the *submit_sm* PDU is set to 1, then this message should replace the undelivered first message. The result is that the subscriber gets the latest message instead of all messages.

Alternatively, a MC administrator may define a specific *service_type* to provide ‘replace-if- present’ functionality. In this case, the replace function can be activated in the *submit_sm* PDU by setting the *service_type* field to the defined value.

For both methods of replacing a message using the *submit_sm* operation, the data contained in the short message found in the MC, whose source and destination addresses and *service_type* match those addresses specified in the latest submit_sm operation, will be replaced with the text contained in the *short_message* field of that latest submit_sm operation.

If the *submit_sm* PDU is used to replace an as yet undelivered message in the MC, and a matching message is not found in the MC, a new short message will be submitted to the MC.

If a matching message is not found when using the *replace_sm* operation, the *replace_sm* operation will not result in a new message being submitted to the MC-a SMPP error will be returned to the ESME in the *replace_sm_resp* PDU.

##### 4.2.8 Message Length

Each network variation is limited to some fixed maximum length. This may be further affected by the data coding scheme. In the case of GSM, a message can have a maximum length of 140 octets (8 bits each). However, if the message is being sent using the standard GSM 7-bit alphabet or one of the supported 7-bit alphabets, 160 characters can be encoded.

The ANSI-41 technologies, CDMA and TDMA, are more complicated in defining limits on the text length. Both technologies encode the text in a generic bearer data field that is also used to populate other optional fields. The restriction on text is based on the amount of optional attributes included with the message.

The MC, depending on configuration, may also reject or truncate messages that exceed the network allowed maximum.

##### 4.2.9 Message Types

##### 4.2.9.1 Registered

The *registered_delivery* field (ref. 4.7.21) allows an ESME request a delivery receipt for the message. Under normal circumstances, a receipt is typically sent to the ESME when the message reached a final delivery state, regardless of whether the message was actually delivered or not. However the *registered_delivery* field provides a number of settings that dictate the requirements for generating the receipt. One such example is the value of 2, which requests a receipt only if the message is not delivered when it reaches its final state.

The following diagram illustrates the use of registered delivery as a means of obtaining a delivery confirmation.
```text
  ┌─────────────────┐            ┌────────────────┐                    ┌─────┐
  │       ESME      │            │ Message Center │                    │ SME │
  └───┬─────────┬───┘            └────────┬───────┘                    └──┬──┘
      │    [Bound_TX]                     │                               │
 [Bound_RX]     │                         │                               │
      │         │─────── submit_sm ──────>│                               │
      │         │<─── submit_sm_resp ─────│                               │
      │                                   │                               │
      │                                   │── Network Delivery Attempt ──>│
      │                                   │<────── NACK (failure) ────────│
      │                                   │                               │
      │                                   │── Network Delivery Attempt ──>│
      │                                   │<─────── ACK (success) ────────│
      │<───── deliver_sm (receipt) ───────│                               │
      │───────── deliver_sm_resp ────────>│                               │
```
**Figure 4-1 Registered Delivery**

##### 4.2.9.2 Scheduled

A scheduled message is one that is not immediately dispatched for delivery to the destination SME. Instead the scheduled date provided with the message (ref. 4.7.23.1) dictates the time that the message will become eligible for delivery. Typical uses of this feature include timed services such as news reports that a user wishes to receive at a certain time. The newsagent ESME may submit batches of messages every few hours, each message scheduled according to the preferences of the recipient.

Another use of scheduled messages is to provide for birthday services or SMS-based reminder applications that pre populate the MC with various scheduled messages for a user.

##### 4.2.9.3 Pre-defined

A pre-defined message is a `canned` message that is provisioned on a MC. The ESME specifies the message by providing its ID in the *sm_default_msg_id* field (ref. 4.7.27). The purpose of the predefined message is to relieve the ESME from specifying the actual text, allowing the operator control over what goes to the subscriber.

SMPP V5.0  SMS Forum 80 of 166

##### 4.2.10 Message Modes

The *esm_class* field (ref. 4.7.12) provides a Message Mode feature, which, if supported on the MC, allows an ESME to select the MC message submission/delivery mechanism. The options are as follows:

- Default Message Mode This can be any of the following three modes, but usually maps to `Store and Forward` mode
- Datagram Mode Message delivery is attempted only once
- Transaction Mode Message delivery is attempted once, with the *submit_sm_resp* or *data_sm_resp* delayed until the delivery attempt has been made.
- Store and Forward Mode The message is stored in the MC message database.
These Message Modes are described in more detail in the following sections.

##### 4.2.10.1 Default Message Mode

The concept of a default message mode relates to scenarios where an ESME does not specifically request a message mode. In this scenario, bits 0-1 of the *esm_class* are both set to 0 and the default MC mode applies.

##### 4.2.10.2 Store and Forward Message Mode

The conventional approach to SMS has been to store the message in a MC storage area (e.g. message database) before forwarding the message for delivery to the recipient SME. With this model, the message remains securely stored until the MC has made all delivery attempts or until the message expires. This mode of messaging is commonly referred to as "store and forward".

**Note:** With store and forward mode, messages can be cancelled, queried or replaced using

the *cancel_sm*, *query_sm* and *replace_sm* operations or submit w/replace mode of *submit_sm*.

The following diagram illustrates store and forward mode. The message is first accepted by the Message Center and acknowledged with the *submit_sm_resp* PDU that is returned to the ESME. The message center then makes an attempt to deliver the message, which fails due to some network error. Several retry attempts may occur until the message is finally delivered to the subscriber. Store and forward is based on the concept of giving the message to the MC and relying on the MC to do a `best effort` attempt to deliver the message to its destination.
```text
    ┌──────┐                   ┌────────────────┐                    ┌─────┐
    │ ESME │                   │ Message Center │                    │ SME │
    └───┬──┘                   └────────┬───────┘                    └──┬──┘
   [Bound_TX]                           │                               │
        │────────── submit_sm ─────────>│                               │
        │<────── submit_sm_resp ────────│                               │
        │                               │── Network Delivery Attempt ──>│
        │                               │<────── NACK (failure) ────────│
        │                               │                               │
        │                               │── Network Delivery Attempt ──>│
        │                               │<─────── ACK (success) ────────│
```
**Figure 4-2 Store and Forward Mode**

##### 4.2.10.3 Datagram Message Mode

The Datagram Message Mode emulates the datagram paradigm used in other data communication protocols such as UDP datagram packet transfer and focuses on high message throughput without the associated secure storage and retry guarantees of Store and Forward Message Mode. In Datagram Message Mode the message originator (i.e. the ESME) does not receive any form of delivery acknowledgement.

In Datagram Message Mode, typical MC functions such as scheduled delivery, registered delivery etc. do not apply. Datagram Message Mode is designed for high throughput applications that may not require the highly secure delivery functionality offered by the Store and Forward message mode. It is ideally suited for applications where the data content is transient in nature, for example, vehicle tracking applications.
```text
    ┌──────┐                   ┌────────────────┐                    ┌─────┐
    │ ESME │                   │ Message Center │                    │ SME │
    └───┬──┘                   └────────┬───────┘                    └──┬──┘
   [Bound_TX]                           │                               │
    ────┼──────── datagram mode ────────┼── one delivery attempt only ──┼────
        │────────── submit_sm ─────────>│                               │
        │<────── submit_sm_resp ────────│                               │
        │                               │── Network Delivery Attempt ──>│
        │                               │<────── NACK (failure) ────────│
    ────┼──── store & forward mode ─────┼ retry until delivered/expired ┼────
        │────────── submit_sm ─────────>│                               │
        │<────── submit_sm_resp ────────│                               │
        │                               │── Network Delivery Attempt ──>│
        │                               │<────── NACK (failure) ────────│
        │                               │── Network Delivery Attempt ──>│
        │                               │<─────── ACK (success) ────────│
```
**Figure 4-3 Datagram Message Mode compare to Store and Forward Mode**

SMPP V5.0  SMS Forum 83 of 166

##### 4.2.10.4 Transaction Message Mode

Transaction Message Mode allows the ESME message originator to receive a form of delivery acknowledgment (that indicates if the message has been successfully or unsuccessfully delivered to the destination MS) within the SMPP response PDU.

Transaction Message Mode is designed for applications that involve real-time messaging where an ESME requires a synchronous end-to-end delivery outcome, without the need for long term MC storage.
```text
    ┌──────┐                   ┌────────────────┐                    ┌─────┐
    │ ESME │                   │ Message Center │                    │ SME │
    └───┬──┘                   └────────┬───────┘                    └──┬──┘
   [Bound_TX]                           │                               │
    ────┼────── transaction mode ───────┼─ resp after delivery outcome ─┼────
        │────────── submit_sm ─────────>│                               │
        │                               │── Network Delivery Attempt ──>│
        │                               │<────── NACK (failure) ────────│
        │<────── submit_sm_resp ────────│                               │
    ────┼────── transaction mode ───────┼──────── using data_sm ────────┼────
        │─────────── data_sm ──────────>│                               │
        │                               │── Network Delivery Attempt ──>│
        │                               │<─────── ACK (success) ────────│
        │<─────── data_sm_resp ─────────│                               │
```
**Figure 4-4 Transaction Mode**

SMPP V5.0  SMS Forum 84 of 166

##### 4.3 Message Delivery Operations

Message delivery operations provide the means of delivering short messages from a MC to an ESME. These messages typically originate from mobile stations.

##### 4.3.1 deliver_sm Operation

The *deliver_sm* is issued by the MC to send a message to an ESME. Using this command, the MC may route a short message to the ESME for delivery.

##### 4.3.1.1 deliver_sm Syntax

##### Field Name Size Type Description Ref

**octets**

command_length 4 Integer Set to overall length of 4.7.4 PDU.

##### command_id 4 Integer 0x00000005 4.7.5

##### command_status 4 Integer 0x00000000 4.7.6

sequence_number 4 Integer Set to a Unique sequence 4.7.24 number. The associated *deliver_sm_resp* PDU will echo this sequence number.

service_type C-Octet The service_type parameter 4.7.25 String can be used to indicate the Var. SMS Application service max 6 associated with the message. Specifying the service_type allows the ESME to avail of enhanced messaging services such as `replace by service_type` or control the teleservice used on the air interface.

Set to NULL if not known by MC

|source_addr_ton|1|Integer|Type of Number for source|4.7.1|
|---|---|---|---|---|
||||address.||
|source_addr_npi|1|Integer|Numbering Plan Indicator for source address.|4.7.2|
|source_addr Var.|max 21|C-Octet String|Address of SME which originated this message.|4.7.29|
|dest_addr_ton|1|Integer|Type of Number for destination|4.7.1|
|dest_addr_npi|1|Integer|Numbering Plan Indicator for destination|4.7.2|
|destination_addr Var.|max 21|C-Octet String|Destination address of this short message For mobile terminated messages, this is the directory number of the recipient MS|4.7.8|
|SMPP V5.0|| SMS Forum||85 of 166|

|Field Name|Size octets|Type|Description|Ref.|
|---|---|---|---|---|
|esm_class|1 protocol_id 1 Integer Protocol Identifier.|Integer|Indicates Message Mode and Message Type Network specific field.|4.7.12 4.7.20|
|priority_flag|1|Integer|Designates the priority level of the message|4.7.19|
|schedule_delivery_time|1 or 17|C-Octet String|The short message is to be scheduled by the receiving MC or ESME for delivery. This field is only applicable if the short message is being forwarded to another MC. In this case it is the time at which the receiving MC should schedule the short message. Set to NULL if not scheduled.|4.7.23.1|
|validity_period 1 or|17|C-Octet String|The validity period of this message. This field is only applicable if this short message is being forwarded to another MC. In this case it specifies how long the receiving MC should retain the SM and continue trying to deliver it. Set to NULL if the current validity period is unavailable.|4.7.23.2|
|registered_delivery|1|Integer|Indicator to signify if a MC delivery receipt or an SME acknowledgement is required.|4.7.21|
|replace_if_present_flag 1 Integer|||Flag indicating if delivered message should replace an existing message.|4.7.22|
|data_coding|1|Integer|Defines the encoding scheme of the short message user data.|4.7.7|

SMPP V5.0  SMS Forum 86 of 166

|Field Name||Size octets||Type||Description||Ref.|
|---|---|---|---|---|---|---|---|---|
|sm_default_msg_id||1||Integer||Indicates the short message to send from a list of pre- defined (‘canned’) short messages stored on the receiving MC. This field is only applicable if this message is being forwarded to another MC. Set to NULL. If not known or applicable||4.7.27|
|sm_length||1||Integer||Length in octets of the short_message user data.||4.7.28|
|short_message Var.||0-255||Octet String||Up to 255 octets of short message user data. The exact physical limit for short_message size may vary according to the underlying network Note: this field is superceded by the message_payload TLV if specified. Ref. 4.8.4.36 Applications which need to send messages longer than 255 octets should use the message_payload TLV. In this case the sm_length field should be set to zero||4.7.26|
|Message Delivery Request TLVs|||Var. TLV|||||4.3.3|
|4.3.1.2 deliver_sm_resp Syntax||Max 65|Table 4-22|deliver_sm String||PDU be set to NULL||4.7.14|

##### Field Name Size Type Description Ref

**octets**

command_length 4 Integer Set to overall length of PDU. 4.7.4

##### command_id 4 Integer 0x8000005 4.7.5

command_status 4 Integer Indicates outcome of 4.7.6 *deliver_sm* request.

sequence_number 4 Integer Set to sequence number of 4.7.24 original *deliver_sm* PDU.

message_id Var. C-Octet This field is unused and should

SMPP V5.0  SMS Forum 87 of 166

||Field Name|Size octets|Type|Description||Ref.||
|---|---|---|---|---|---|---|---|
||Message Delivery Response TLVs 4.3.2 data_sm Operation The data_sm 4.3.3 Message Delivery Request TLVs operations.|Var. TLV Table 4-23 For detail on the specification of data_sm, refer to section 4.2.2. The following table lists TLVs appropriate for message delivery using|deliver_sm_resp|PDU operation is symmetrically used for delivery as it is used to submit messages. deliver_sm or||4.3.4 data_sm||
||TLV Name callback_num callback_num_atag callback_num_pres_ind dest_addr_np_country dest_addr_np_information dest_addr_np_resolution dest_addr_subunit dest_network_id dest_node_id dest_subaddress dest_port dpf_result|Description back number. instances of the callback_num_atag callback_num instances of the callback_num user data is intended.|multiple call-back addresses. callback_num. callback_num callback_num_pres_ind. E.164 information to the operator country code Number portability query indicator Identification of destination network Identification of destination node The sub-address of the message destination. should be present for WAP applications.|A call-back number associated with the short message. This parameter can be included a number of times for Associates a displayable alphanumeric tag with the call- If this parameter is present and there are multiple parameter then this parameter must occur an equal number of instances and the order of occurrence determines the particular which corresponds to a particular Defines the call-back number presentation and screening. 4.8.4.17 If this parameter is present and there are multiple parameter then this parameter must occur an equal number of instances and the order of occurrence determines the particular which corresponds to a particular Number portability information for the destination address 4.8.4.21 The subcomponent in the destination device for which the 4.8.4.23 Indicates the application port number associated with the destination address of the message. This parameter Indicates whether the Delivery Pending Flag was set.||Ref. 4.8.4.15 4.8.4.16 4.8.4.20 4.8.4.22 4.8.4.25 4.8.4.27 4.8.4.28 4.8.4.30 4.8.4.32||

SMPP V5.0  SMS Forum 88 of 166

|TLV Name|Description|Ref.|
|---|---|---|
|its_reply_type|The MS user’s reply method to an SMS delivery message 4.8.4.33 received from the network is indicated and controlled by this parameter.||
|its_session_info language_indicator|Session control information for Interactive Teleservice. Indicates the language of an alphanumeric text message. 4.8.4.35|4.8.4.34|
|message_payload|Contains the extended short message user data. Up to|4.8.4.36|

##### 64K octets can be transmitted

Note: The short message data should be inserted in either

the *short_message* or *message_payload* fields. Both fields should not be used simultaneously.

The *sm_length* field should be set to zero if using the *message_payload* parameter.

Note: In the case of *data_sm*, the *message_payload* TLV

is the only means of specifying text.

|message_state|Should be present for MC Delivery Receipts and|4.8.4.37|
|---|---|---|
||Intermediate Notifications.||
|network_error_code|May be present for delivery receipts and Intermediate Notifications|4.8.4.42|
|payload_type|defines the type of payload (e.g. WDP, WCMP, etc.).|4.8.4.44|
|privacy_indicator|Indicates the level of privacy associated with the message.|4.8.4.45|
|receipted_message_id sar_msg_ref_num|MC message ID of message being receipted. Should be present for MC Delivery Receipts and Intermediate Notifications. The reference number for a particular concatenated short 4.8.4.48 message.|4.8.4.47|
|sar_segment_seqnum|Indicates the sequence number of a particular short message fragment within the concatenated short message.|4.8.4.49|
|sar_total_segments|Indicates the total number of short message segments within the concatenated short message.|4.8.4.50|
|source_addr_subunit|The subcomponent in the destination device, which created the user data.|4.8.4.54|
|source_network_id|Identification of source network|4.8.4.56|
|source_node_id|Identification of source node|4.8.4.58|
|source_port|Indicates the application port number associated with the source address of the message. This parameter should be present for WAP applications.|4.8.4.59|
|source_subaddress user_message_reference ESME assigned message reference number.|The sub-address of the message originator.|4.8.4.60 4.8.4.62|
|user_response_code|A user response code. The actual response codes are implementation specific.|4.8.4.63|
|ussd_service_op SMPP V5.0|This parameter is used to identify the required USSD Service type when interfacing to a USSD system. Table 4-24 Message Delivery Request TLVs|4.8.4.64 89 of 166|

#####  SMS Forum

##### 4.3.4 Message Delivery Response TLVs

|TLV Name|Description|Ref.|
|---|---|---|
|additional_status_info_text|ASCII text giving a description of the meaning of the4.8.4.1 response.||
|delivery_failure_reason|Include to indicate reason for delivery failure.|4.8.4.19|
|network_error_code|Error code specific to a wireless network.|4.8.4.42|

**Table 4-25 Message Delivery Response TLVs**

##### 4.3.5 Delivery Message Types

There are a number of different types of message that can be delivered to an ESME Receiver or Transceiver. These are as follows:

- Normal Message This can be any normal message originally submitted by a mobile phone or another ESME.
- MC Delivery Receipt
- Intermediate Notification
- SME User/Manual Acknowledgement
- SME Delivery Acknowledgement
- Conversation Abort
- MC-MC Handover Message

##### 4.3.5.1 MC Delivery Receipt

This message type is used to carry a MC delivery receipt. The MC, on detecting the final state of a registered message (ref. 4.7.21 & 4.7.15), would normally generate a new receipt message addressed to the originator of the first message. The MC Delivery Receipt is then delivered to the ESME in a *deliver_sm* or *data_sm* operation.

The following fields are relevant in the *deliver_sm* and *data_sm* operations when used for transmitting delivery receipts.

- source address (i.e. *source_addr_ton, source_addr_npi, source_addr*) The source address will be taken from the destination address of the original short message, which generated the delivery receipt. The receipt appears as if it emanated from the recipient of the original registered message.
- destination address (i.e. *dest_addr_ton, dest_addr_npi, destination_addr*) The destination address will be taken from the source address of the original short message, which generated the delivery receipt. The receipt is addressed to the SME that originally sent the registered message.
- *esm_class* Bit 2 of the *esm_class* is set to 1 to indicate that the message is a delivery receipt.
- *message_state* TLV This TLV indicates the final state of the original message.
- *network_error_code* TLV
- *receipted_message_id* TLV
SMPP V5.0  SMS Forum 90 of 166

Note: Many pre-V3.4 interfaces and Message Centers supporting V3.3 are likely to have a

means of passing receipt information within the *short_message* field. The format specifics of this information is product specific and beyond the scope of this specification.

Note: The returning of a message receipt is dependent on the value set in the

*registered_delivery* field of the original message. This can be configured for non-delivery and delivery-only scenarios that can result in circumstances where the receipt will not be returned. See section 4.7.21 for more information on these settings.

##### 4.3.5.2 Intermediate Notification

An intermediate notification is a special form of message that the MC may send to an ESME for a mobile terminated message delivery. It provides an intermediate status of a message delivery attempt.

Typical uses are to report the outcome of delivery attempts made during the message’s retry lifetime within the MC. This could be used to track the various reasons why a message is not delivered to its destination and use this to profile the subscriber’s availability.

In such cases the following TLVs would be of importance:

- *message_state* TLV
- *network_error_code* TLV
- *receipted_message_id* TLV
However, support for Intermediate Notification functionality is specific to the MC implementation and the MC Service Provider and is beyond the scope of this specification.

##### 4.3.5.3 SME Delivery Acknowledgement

Despite its name, an SME Delivery Acknowledgement is not an indication that the short message has arrived at the SME, but rather an indication from the recipient SME that the user has read the short message.

For an MS-based SME, an SME Delivery Acknowledgement is sent when the MS user or MS application has read the message from the SMS storage unit.

For a fixed SME (i.e. ESME) the circumstances in which an SME Delivery Acknowledgement may be sent are beyond the scope of this specification.

**Note:** SME Delivery Acknowledgements are supported in TDMA and CDMA only.

##### 4.3.5.4 SME Manual/User Acknowledgement

A Manual/User Acknowledgement is an application generated reply message sent in response to an application request message. For example, this message type could contain a selected menu item number from a menu list sent in an application request message.

**Note:** SME Manual/User Acknowledgements are supported in TDMA and CDMA only.

##### 4.3.5.5 Conversation Abort

This message type is unique to Interactive Teleservice defined by the Korean CDMA Carriers Organization. It is sent by a MS-based SME to indicate the unexpected termination of an interactive session. A Conversation Abort may be carried in a *deliver_sm* or *data_sm* PDU.

SMPP V5.0  SMS Forum 91 of 166

##### 4.4 Message Broadcast Operations

Message Broadcast operations provice Cell Broadcast services to ESMEs.

**Note:** Message broadcast operations are applicable to Cell Broadcast Centres only or to

SMSCs with integrated CBC functionality. Although the term ESME is used through-out this section, the term Cell Broadcast Entity (CBE) is inferred.

##### 4.4.1 broadcast_sm Operation

This operation is issued by the ESME to submit a message to the Message Centre for broadcast to a specified geographical area or set of geographical areas.

##### 4.4.1.1 broadcast_sm Syntax

|Field Name|Size|Type|Description|Ref.|
|---|---|---|---|---|
||octets||||
|command_length|4|Integer|Set to overall length of PDU.|4.7.4|
|command_id|4|Integer|0x00000111|4.7.5|
|command_status|4|Integer|0x00000000|4.7.6|
|sequence_number|4|Integer|Set to a Unique sequence number. The associated broadcast_sm_resp PDU will echo this sequence number.|4.7.24|

service_type Var. C-Octet The service_type 4.7.25 max 6 String parameter can be used to indicate the SMS Application service associated with the message. Specifying the service_type allows the ESME to avail of enhanced messaging services such as `replace by service_type` or control the teleservice used on the air interface.

Set to NULL for default MC settings

source_addr_ton 1 Integer Type of Number for source 4.7.1 address.

If not known, set to NULL (Unknown).

source_addr_npi 1 Integer Numbering Plan Indicator 4.7.2 for source address.

If not known, set to NULL (Unknown).

SMPP V5.0  SMS Forum 92 of 166

##### Field Name Description

||Size|Type||Ref.|
|---|---|---|---|---|
||octets||||
|source_addr Var.|max 21|C-Octet String|Address of SME, which originated this message. If not known, set to NULL (Unknown).|4.7.29|
|message_id Var.|max 65|C-Octet String|If using broadcast_sm to replace a message, previously submitted for|4.7.14|

broadcast, then set *message_id* to the MC assigned message ID allocated to the original message and returned in the *broadcast_sm_resp* (to the original *broadcast_sm* request).

Note:

For "broadcast replace", either the *message_id* or the *user_message_reference* field should be used. Both fields must not be used simultaneously.

##### Set to NULL

- if not using MC message ID in *broadcast_sm* to replace a message, previously submitted for broadcast.
- if setting *user_message_* *reference* TLV.

|priority_flag|1|Integer|Designates the propriety|4.7.19|
|---|---|---|---|---|
||||level of the message||
|schedule_delivery_time 1 or|17|C-Octet String|The short message is to be scheduled by the MC for|4.7.23.1|

delivery.

Set to NULL for immediate message broadcast.

SMPP V5.0  SMS Forum 93 of 166

|Field Name|Size octets|Type|Description|Ref.|
|---|---|---|---|---|
|validity_period 1 or|17|C-Octet String ‘|The validity period of this 4.7.23.2 message. Set to NULL to specify that a broadcast_rep_num’ parameter and a ‘ broadcast_frequency_interval ’ parameter have been specified from which a default value should be derived.||
|replace_if_present_flag 1 Integer|||Flag indicating if the submitted message should replace an existing message which has: (1) MC message ID matching the ID supplied in the message_id field (2) or ESME assigned message reference number supplied in the user_message_reference field.|4.7.22|
|data_coding|1|Integer|Defines the encoding scheme of the short message user data.|4.7.7|
|sm_default_msg_id|1|Integer|Indicates the short message 4.7.27 to send from a list of pre- defined (‘canned’) short messages stored on the MC. If not using a MC canned message, set to NULL.||
|broadcast_area_identifier|Var.|TLV|Identifies the target Broadcast Area(s) for the requested message broadcast. This parameter can be included a number of times for multiple target Broadcast Areas(s).|4.8.4.4|
|broadcast_content_type Var. TLV|||Specifies the content type of the message.|4.8.4.8|
|broadcast_rep_num|Var.|TLV|This field indicates the number of repeated broadcasts of a message requested by the submitter.|4.8.4.13|
|broadcast_frequency_ interval|Var.|TLV|This field indicates the frequency interval at which the broadcasts of a message should be repeated.|4.8.4.11|

SMPP V5.0  SMS Forum 94 of 166

|Field Name|Size octets|Type|Description|Ref.|
|---|---|---|---|---|
|Broadcast Request Optional TLVs|Var. TLV|||4.4.2|

**Table 4-26 *broadcast_sm* PDU**

SMPP V5.0  SMS Forum 95 of 166

##### 4.4.1.2 broadcast_sm_resp Syntax

##### Field Name Size Type Description Ref

**octets**

command_length 4 Integer Set to overall length of PDU. 4.7.4

##### command_id 4 Integer 0x80000111 4.7.5

command_status 4 Integer Indicates outcome of 4.7.6 *broadcast_sm* request.

sequence_number 4 Integer Set to sequence number of 4.7.24 original *broadcast_sm* PDU.

message_id Var. C-Octet This field contains the MC 4.7.14 max 65 String message ID of the submitted message. It may be used at a later stage to perform subsequent operations on the message.

Broadcast Response Var. TLV 4.4.3 Optional TLVs

**Table 4-27 *broadcast_sm_resp* PDU**

##### 4.4.2 Broadcast Request Optional TLVs

This section lists optional TLVs that may be additionally specified in a broadcast_sm PDU

|Field Name|Size|Type|Description|Ref.|
|---|---|---|---|---|
||octets||||
|alert_on_msg_delivery|Var.|TLV|Request an MS alert signal be invoked on message delivery.|4.8.4.2|
|broadcast_channel_ indicator|Var.|TLV|Specifies the Cell Broadcast channel that should be used for broadcasting the message.|4.8.4.7|
|broadcast_content_type_ info|Var.|TLV|This parameter contains additional free format information specific to the broadcast_content_type.|4.8.4.6|
|broadcast_message_class|Var.|TLV|This field specifies the class of 4.8.4.12 message to be broadcast.||
|broadcast_service_group Var.||TLV|This parameter is used to specify special target groups for broadcast information.|4.8.4.14|
|callback_num|Var.|TLV|A call-back number|4.8.4.15|

associated with the short message.

This parameter can be included a number of times for multiple call-back addresses.

SMPP V5.0  SMS Forum 96 of 166

|Field Name||Size octets||Type||Description||Ref.|
|---|---|---|---|---|---|---|---|---|
|callback_num_atag||Var.||TLV||Associates a displayable alphanumeric tag with the call- back number. If this parameter is present and there are multiple instances of the callback_num parameter then this parameter must occur an equal number of instances and the order of occurrence determines the particular callback_num_atag which corresponds to a particular callback_num.||4.8.4.16|
|callback_num_pres_ind||Var.||TLV||Defines the call-back number 4.8.4.17 presentation and screening. If this parameter is present and there are multiple instances of the callback_num parameter then this parameter must occur an equal number of instances and the order of occurrence determines the particular callback_num_pres_ind which corresponds to a particular callback_num.|||
|dest_addr_subunit||Var.||TLV||The subcomponent in the destination device for which the user data is intended.||4.8.4.23|
|dest_subaddress||Var.||TLV||The sub address of the message destination.||4.8.4.28|
|dest_port||Var.||TLV||Indicates the application port number associated with the destination address of the message. This parameter should be present for WAP applications.||4.8.4.30|
|display_time||Var.||TLV||Provides the receiving MS with a display time associated with the message.||4.8.4.31|
|language_indicator||Var.||TLV||Indicates the language of an alphanumeric text message.||4.8.4.35|
|message_payload||Var.||TLV||Contains the extended short message user data. Up to 64K octets can be transmitted.||4.8.4.36|
|ms_validity||Var.||TLV||Indicates validity information for this message to the recipient MS.||4.8.4.41|
|payload_type Var. TLV Defi||||||nes the type of payload (e.g. WDP, WCMP, etc.).||4.8.4.44|

SMPP V5.0  SMS Forum 97 of 166

|Field Name||Size octets||Type||Description||Ref.|
|---|---|---|---|---|---|---|---|---|
|privacy_indicator||Var.||TLV||Indicates the level of privacy associated with the message.||4.8.4.45|
|sms_signal||Var.||TLV||Indicates the alerting mechanism when the message is received by an MS.||4.8.4.53|
|source_addr_subunit||Var.||TLV||The subcomponent in the destination device, which created the user data.||4.8.4.54|
|source_port||Var.||TLV||Indicates the application port number associated with the source address of the message. This parameter should be present for WAP applications.||4.8.4.59|
|source_subaddress||Var.||TLV||The sub address of the message originator.||4.8.4.60|
|user_message_reference||Var.||TLV||ESME assigned message reference number. This identifier is used to identify the new message, or in the case of "broadcast replace" (replacing a message, previously submitted for broadcast) set user_message_reference to the ESME assigned message reference, allocated to the original message in the original broadcast_sm request. Note: For broadcast replace, either the message_id or the user_message_reference field should be used. Both fields must not be used simultaneously.||4.8.4.62|

**Table 4-28 Broadcast Request Optional TLVs**

SMPP V5.0  SMS Forum 98 of 166

##### 4.4.3 Broadcast Response Optional TLVs

|Field Name||Size octets||Type||Description||Ref.|
|---|---|---|---|---|---|---|---|---|
|broadcast_error_status||Var.||TLV||This field will indicate the nature of the failure associated with the broadcast request for the indicated area. If this parameter is present and there are multiple instances of the failed_broadcast_area_identifier(s) parameter then this parameter must occur an equal number of instances and the order of occurrence determines the particular broadcast_error_status, which corresponds to a particular failed_broadcast_area_ identifier(s).||4.8.4.10|
|failed_broadcast_area_ identifier||Var.||TLV||Identifies one or more target Broadcast Area(s) for which the requested message broadcast has failed to be accepted by the Service Centre. This parameter can be included a number of times for multiple failed target Broadcast Areas(s).||4.8.4.4|

**Table 4-29 Broadcast Response Optional TLVs**

##### 4.4.4 Message Replacement with broadcast_sm

The *broadcast_sm* operation can be used to replace an existing message, which has been previously submitted to the Message Centre.

Setting the *replace_if_present flag* to 1 activates this mode. Additionally it is necessary to supply a value for the *message_id* parameter, or provide the *user_message_reference* TLV. This is required to identify the previous message by id.

If the MC cannot replace the previous message, the operation will fail. This differs from *submit_sm* w/replace where a new message is submitted, should the replace attempt fail.

SMPP V5.0  SMS Forum 99 of 166

##### 4.5 Ancillary Submission Operations

Ancillary submission operations provide additional management of messages submitted by ESMEs. This includes cancellation, querying and replacement of messages.

##### 4.5.1 cancel_sm Operation

This command is issued by the ESME to cancel one or more previously submitted short messages that are pending delivery. The command may specify a particular message to cancel, or all messages matching a particular source, destination and *service_type*.

If the *message_id* is set to the ID of a previously submitted message, then provided the source address supplied by the ESME matches that of the stored message, that message will be cancelled.

If the *message_id* is NULL, all outstanding undelivered messages with matching source and destination addresses (and *service_type* if specified) are cancelled.

Where the original *submit_sm, data_sm* or *submit_multi* ‘source address’ is defaulted to NULL, then the source address in the *cancel_sm* command should also be NULL.

##### 4.5.1.1 cancel_sm Syntax

|Field Name|Size|Type|Description|Ref.|
|---|---|---|---|---|
||octets||||
|command_length|4|Integer|Set to overall length of PDU.|4.7.4|
|command_id|4|Integer|0x00000008|4.7.5|
|command_status|4|Integer|0x00000000|4.7.6|
|sequence_number|4|Integer|Set to a unique sequence number. The associated cancel_sm_resp PDU should echo the same sequence number.|4.7.24|

service_type Var. C-Octet Set to indicate SMS Application service, 4.7.25 max 6 String if cancellation of a group of application service messages is desired.

##### Otherwise set to NULL

message_id Var. C-Octet Message ID of the message to be 4.7.14 max 65 String cancelled. This must be the MC assigned Message ID of the original message.

Set to NULL if cancelling a group of messages.

source_addr_ton 1 Integer Type of Number of message originator. 4.7.1 This is used for verification purposes, and must match that supplied in the original message submission request PDU.

##### If not known, set to NULL

SMPP V5.0  SMS Forum 100 of 166

|Field Name|Size octets|Type|Description|Ref.|
|---|---|---|---|---|
|source_addr_npi|1|Integer|Numbering Plan Identity of message originator. This is used for verification purposes, and must match that supplied in the original message submission request PDU. If not known, set to NULL.|4.7.2|
|source_addr Var.|max 21|C-Octet String|Source address of message(s) to be cancelled. This is used for verification purposes, and must match that supplied in the original message submission request PDU(s). If not known, set to NULL.|4.7.29|
|dest_addr_ton|1|Integer|Type of number of destination SME address of the message(s) to be cancelled. This is used for verification purposes, and must match that supplied in the original message submission request PDU (e.g. submit_sm). May be set to NULL when the message_id is provided.|4.7.1|
|dest_addr_npi|1|Integer|Numbering Plan Indicator of destination SME address of the message(s) to be cancelled. This is used for verification purposes, and must match that supplied in the original message submission request PDU. May be set to NULL when the message_id is provided.|4.7.2|
|destination_addr Var.|max 21|C-Octet String|Destination address of message(s) to be cancelled. This is used for verification purposes, and must match that supplied in the original message submission request PDU. May be set to NULL when the message_id is provided.|4.7.8|

**Table 4-30 *cancel_sm* PDU**

SMPP V5.0  SMS Forum 101 of 166

##### 4.5.1.2 cancel_sm_resp Syntax

The *cancel_sm_resp* PDU is used to reply to a *cancel_sm* request. It comprises the SMPP message header only*.*

|Field Name|Size octets|Type|Description|Ref.|
|---|---|---|---|---|
|command_length|4|Integer|Set to overall length of PDU.|4.7.4|
|command_id|4|Integer|0x80000008|4.7.5|
|command_status|4|Integer|Indicates outcome of cancel_sm request.|4.7.6|
|sequence_number|4|Integer|Set to sequence number of cancel_sm|4.7.24|
||||PDU.||

**Table 4-31 *cancel_sm_resp* PDU**

##### 4.5.2 query_sm Operation

This command is issued by the ESME to query the status of a previously submitted short message.

The matching mechanism is based on the MC assigned *message_id* and source address. Where the original *submit_sm, data_sm* or *submit_multi* ‘source address’ was defaulted to NULL, then the source address in the *query_sm* command should also be set to NULL.

##### 4.5.2.1 query_sm Syntax

|Field Name|Size octets|Type|Description|Ref.|
|---|---|---|---|---|
|command_length|4|Integer|Set to overall length of PDU|4.7.4|
|command_id|4|Integer|0x00000003|4.7.5|
|command_status|4|Integer|0x00000000|4.7.6|
|sequence_number|4|Integer|Set to a unique sequence number. The associated query_sm_resp PDU should echo the same sequence number|4.7.24|

message_id Var. C-Octet Message ID of the message whose state 4.7.14 Max 65 String is to be queried. This must be the MC assigned Message ID allocated to the original short message when submitted to the MC by the *submit_sm*, *data_sm* or *submit_multi* command, and returned in the response PDU by the MC.

source_addr_ton 1 Integer Type of Number of message originator. 4.7.1 This is used for verification purposes, and must match that supplied in the original request PDU (e.g. *submit_sm*).

##### If not known, set to NULL

SMPP V5.0  SMS Forum 102 of 166

source_addr_npi Integer Numbering Plan Identity of message 4.7.2 originator.

This is used for verification purposes, and must match that supplied in the original message submission request PDU.

##### If not known, set to NULL

source_addr Var. C-Octet Address of message originator. 4.7.29 Max 21 String This is used for verification purposes, and must match that supplied in the original request PDU (e.g. *submit_sm*).

##### If not known, set to NULL

**Table 4-32 *query_sm* PDU**

##### 4.5.2.2 query_sm_resp Syntax

|Field Name|Size octets|Type|Description|Ref.|
|---|---|---|---|---|
|command_length|4|Integer|Set to overall length of PDU.|4.7.4|
|command_id|4|Integer|0x8000003|4.7.5|
|command_status|4|Integer|Indicates outcome of query_sm request|4.7.6|
|sequence_number|4|Integer|Set to sequence number of original query_sm PDU.|4.7.24|

message_id Var. C-Octet MC Message ID of the message whose 4.7.14 max 65 String state is being queried.

final_date 1 or 17 C-Octet Date and time when the queried 4.7.23.3 String message reached a final state. For messages, which have not yet reached a final state, this field will contain a single NULL octet.

message_state 1 Integer Specifies the status of the queried short 4.7.15 message.

error_code 1 Integer Where appropriate this holds a network error code defining the reason for failure of message delivery.

The range of values returned depends on the underlying telecommunications network.

**Table 4-33 *query_sm_resp* PDU**

SMPP V5.0  SMS Forum 103 of 166

##### 4.5.3 replace_sm Operation

This command is issued by the ESME to replace a previously submitted short message that is pending delivery. The matching mechanism is based on the *message_id* and source address of the original message.

Where the original *submit_sm* ‘source address’ was defaulted to NULL, then the source address in the *replace_sm* command should also be NULL.

##### 4.5.3.1 replace_sm Syntax

|Field Name|Size|Type|Description|Ref.|
|---|---|---|---|---|
||octets||||
|command_length|4|Integer|Set to overall length of PDU.|4.7.4|
|command_id|4|Integer|0x00000007|4.7.5|
|command_status|4|Integer|0x00000000|4.7.6|
|sequence_number|4|Integer|Set to a Unique sequence number. The associated replace_sm_resp PDU will echo this sequence number.|4.7.24|

message_id Var. C-Octet Message ID of the 4.7.14 Max 65 String message to be replaced. This must be the MC assigned Message ID allocated to the original short message when submitted to the MC by the *submit_sm*, *data_sm* or *submit_multi* command, and returned in the response PDU by the MC.

source_addr_ton 1 Integer Type of Number of message 4.7.1 originator. This is used for verification purposes, and must match that supplied in the original request PDU (e.g. *submit_sm*).

##### If not known, set to NULL

source_addr_npi 1 Integer Numbering Plan Indicator 4.7.2 for source address of original message.

If not known, set to NULL (Unknown).

source_addr Var. C-Octet Address of SME, which 4.7.29 max 21 String originated this message.

If not known, set to NULL (Unknown).

SMPP V5.0  SMS Forum 104 of 166

|Field Name|Size octets|Type|Description|Ref.|
|---|---|---|---|---|
|schedule_delivery_time|1 or 17|C-Octet String|New scheduled delivery time for the short message. Set to NULL to preserve the original scheduled delivery time|4.7.23.1|
|validity_period|1 or 17|C-Octet String|New expiry time for the short message. Set to NULL to preserve the original validity period setting.|4.7.23.2|
|registered_delivery|1|Integer|Indicator to signify if a MC delivery receipt, user/manual or delivery ACK or intermediate notification is required.|4.7.21|
|sm_default_msg_id|1|Integer|Indicates the short message to send from a list of pre- defined (‘canned’) short messages stored on the MC. If not using a MC canned message, set to NULL.|4.7.27|
|sm_length|1|Integer|Length in octets of the short_message user data.|4.7.28|
|short_message Var.|0-255|Octet String|Up to 255 octets of short message user data. The exact physical limit for short_message size may vary according to the underlying network Note: this field is superceded by the message_payload TLV if specified. Ref. 4.8.4.36 Applications which need to send messages longer than 255 octets should use the message_payload TLV. In this case the sm_length field should be set to zero|4.7.26|
|Message Replacement Request TLVs|Var. TLV|||4.5.3.3|

**Table 4-34 *replace_sm* PDU**

SMPP V5.0  SMS Forum 105 of 166

##### 4.5.3.2 replace_sm_resp Syntax

|4.5.3.3 Message Replacement TLVs|||||||||
|---|---|---|---|---|---|---|---|---|
|Field Name||Size octets||Type||Description||Ref.|
|message_payload||Var.||TLV||Contains the extended short message user data. Up to 64K octets can be transmitted. Note: The short message data should be inserted in either the short_message or message_payload fields. Both fields should not be used simultaneously. The sm_length field should be set to zero if using the message_payload parameter.||4.8.4.36|

##### Field Name Size Type Description Ref

**octets**

command_length 4 Integer Set to overall length of PDU. 4.7.4

##### command_id 4 Integer 0x80000007 4.7.5

command_status 4 Integer Indicates outcome of 4.7.6 *replace_sm* request.

sequence_number 4 Integer Set to sequence number of 4.7.24 original *replace_sm* PDU.

**Table 4-35 *replace_sm_resp* PDU**

**Table 4-36 Message Replacement TLVs**

SMPP V5.0  SMS Forum 106 of 166

##### 4.6 Ancillary Broadcast Operations

##### 4.6.1 query_broadcast_sm Operation

This command is issued by the ESME to query the status of a previously submitted broadcast message. The message can be queried either on the basis of the Message Center assigned reference *message_id* returned in the *broadcast_sm_resp* or by the ESME assigned message reference number *user_message_reference* as indicated in the *broadcast_sm* operation associated with that message.

Note: Where the broadcast is queried on the basis of the ESME assigned message

reference *user_message_reference* this should be qualified within the service by the *system_id* and/or the *system_type* associated with the *query_broadcast_sm* operation (specified in the bind operation). If more than one message with the same *user_message_reference* value is present in the Message Center, the details of the most recently submitted message with the specified *user_message_reference* value will be returned in the *query_broadcast_sm_resp*.

##### 4.6.1.1 query_broadcast_sm Syntax

|Field Name|Size|Type|Description|Ref.|
|---|---|---|---|---|
||octets||||
|command_length|4|Integer|Set to overall length of PDU.|4.7.4|
|command_id|4|Integer|0x00000112|4.7.5|
|command_status|4|Integer|0x00000000|4.7.6|
|sequence_number|4|Integer|Set to a Unique sequence number. The associated query_broadcast_sm_resp PDU will echo this sequence number.|4.7.24|

message_id Var. C-Octet Message ID of the message 4.7.14 max 65 String to be queried. This must be the MC assigned Message ID allocated to the original short message when submitted to the MC by the *broadcast_sm* command, and returned in the response PDU by the MC.

Set to NULL if setting *user_message_reference*.

source_addr_ton 1 Integer Type of Number for source 4.7.1 address.

If not known, set to NULL (Unknown).

source_addr_npi 1 Integer Numbering Plan Indicator for 4.7.2 source address.

If not known, set to NULL (Unknown).

SMPP V5.0  SMS Forum 107 of 166

|Field Name||Size octets||Type||Description||Ref.|
|---|---|---|---|---|---|---|---|---|
|source_addr Var.||max 21||C-Octet String||Address of SME which originated this message. If not known, set to NULL (Unknown).||4.7.29|
|Query Broadcast Request TLVs||Var. TLV||||||4.6.1.2|
|4.6.1.2||Table 4-37 Query Broadcast Request Optional TLVs||query_broadcast_sm||PDU|||
|Field Name||Size octets||Type||Description||Ref.|
|user_message_reference||Var.||TLV||ESME assigned message reference number.||4.8.4.62|
|4.6.1.3||max 65||Table 4-38 Query Broadcast Optional TLVs|MC.|message. This must be the MC assigned Message ID allocated to the original short message when submitted to the MC by the broadcast_sm, command, and returned in the broadcast_sm_resp PDU by the||4.7.14|
|message_state||Var.||TLV||This field indicates the current status of the broadcast message.||4.7.15|

***query_broadcast_sm_resp* Syntax**

##### Field Name Size Type Description Ref

**octets**

command_length 4 Integer Set to overall length of PDU. 4.7.4

##### command_id 4 Integer 0x80000112 4.7.5

command_status 4 Integer Indicates outcome of 4.7.6 *query_broadcast_sm* request.

sequence_number 4 Integer Set to sequence number of 4.7.24 original *query_broadcast_sm* PDU.

message_id Var. Integer Message ID of the queried

SMPP V5.0  SMS Forum 108 of 166

|Field Name||Size octets||Type||Description||Ref.|
|---|---|---|---|---|---|---|---|---|
|broadcast_area_identifier Var. TLV|||||the|Identifies one or more target Broadcast Area(s) for which the status information applies. The number of instances of this parameter will be exactly equal to the number of occurrences of broadcast_area_identifiers parameter in the corresponding broadcast_sm.||4.8.4.4|
|broadcast_area_success||Var.||TLV||The success rate indicator, defined as the ratio of the number of BTSs that accepted the message and the total number of BTSs that should have accepted the message, for a particular broadcast_area_identifier.||4.8.4.5|
|Query Broadcast Response TLVs||Var. TLV||||||4.6.1.4|
|4.6.1.4||Table 4-39 Query Broadcast Response Optional TLVs||query_broadcast_sm_resp||PDU|||
|Field Name||Size octets||Type||Description||Ref.|
|broadcast_end_time||Var.||TLV||The date and time at which the broadcasting state of this message was set to terminated in the Message Centre.||4.8.4.9|
|user_message_reference||Var.||TLV||ESME assigned message reference number.||4.8.4.62|

SMPP V5.0  SMS Forum 109 of 166

##### 4.6.2 cancel_broadcast_sm Operation

This command is issued by the ESME to cancel a broadcast message which has been previously submitted to the Message Centre for broadcast via *broadcast_sm* and which is still pending delivery.

If the *message_id* is set to the ID of a previously submitted message, then provided the source address supplied by the ESME matches that of the stored message, that message will be cancelled.

If the *message_id* is NULL, all outstanding undelivered messages with matching source and destination addresses (and *service_type* if specified) are cancelled.

If the *user_message_reference* is set to the ESME-assigned reference of a previously submitted message, then provided the source address supplied by the ESME matches that of the stored message, that message will be cancelled.

Where the original *broadcast_sm* ‘source address’ was defaulted to NULL, then the source address in the *cancel_broadcast_sm* command should also be NULL.

##### 4.6.2.1 cancel_broadcast_sm Syntax

|Field Name|Size|Type|Description|Ref.|
|---|---|---|---|---|
||octets||||
|command_length|4|Integer|Set to overall length of PDU.|4.7.4|
|command_id|4|Integer|0x00000113|4.7.5|
|command_status|4|Integer|0x00000000|4.7.6|
|sequence_number|4|Integer|Set to a Unique sequence number. The associated cancel_broadcast_sm_ resp PDU will echo this sequence number.|4.7.24|

service_type Var. C-Octet Set to indicate CBS 4.7.25 max 6 String Application service, if cancellation of a group of application service messages is desired.

##### Otherwise set to NULL

message_id Var. C-Octet Message ID of the message 4.7.14 max 65 String to be cancelled. This must be the MC assigned Message ID of the original message.

Set to NULL if setting *user_message_reference*.

source_addr_ton 1 Integer Type of Number of message 4.7.1 originator. This is used for verification purposes, and must match that supplied in the original message submission request PDU.

If not known, set to NULL (Unknown).

SMPP V5.0  SMS Forum 110 of 166

##### Field Name Size Type Description Ref

**octets**

source_addr_npi Integer Numbering Plan Identity of 4.7.2 message originator.

This is used for verification purposes, and must match that supplied in the original message submission request PDU.

If not known, set to NULL (Unknown).

source_addr Var. C-Octet Source address of message 4.7.29 max 21 String to be cancelled. This is used for verification purposes, and must match that supplied in the original message submission request PDU.

If not known, set to NULL (Unknown).

**Table 4-40 *cancel_broadcast_sm* PDU**

##### 4.6.2.2 Cancel Broadcast Optional TLVs

|Field Name|Size|Type|Description|Ref.|
|---|---|---|---|---|
||octets||||
|broadcast_content_type Var. TLV|||Specifies the content type of the message.|4.8.4.8|
|user_message_reference|Var.|TLV|ESME assigned message|4.8.4.62|

reference number.

Note: The *message_id* field

should be set to NULL if using the *user_message_reference* TLV.

SMPP V5.0  SMS Forum 111 of 166

##### 4.6.2.3 cancel_broadcast_sm_resp Syntax

##### Field Name Size Type Description Ref

**octets**

command_length 4 Integer Set to overall length of PDU. 4.7.4

##### command_id 4 Integer 0x80000113 4.7.5

command_status 4 Integer Indicates outcome of 4.7.6 *cancel_broadcast_sm* request.

sequence_number 4 Integer Set to sequence number of 4.7.24 original *cancel_broadcast_sm* PDU.

**Table 4-41 *cancel_broadcast_sm_resp* PDU**

SMPP V5.0  SMS Forum 112 of 166

##### 4.7 PDU Field Definitions

##### 4.7.1 addr_ton, source_addr_ton, dest_addr_ton, esme_addr_ton

These fields define the Type of Number (TON) to be used in the SME address parameters. The following TON values are defined:

|TON|Value|
|---|---|
|Unknown 00000000||
|International 00000001||
|National 00000010||
|Network Specific|00000011|
|Subscriber Number Alphanumeric 00000101 Abbreviated 00000110 All other values reserved|00000100|

**Table 4-42 TON Values**

##### 4.7.2 addr_npi, source_addr_npi, dest_addr_npi, esme_addr_npi

These fields define the Numeric Plan Indicator (NPI) to be used in the SME address parameters. The following NPI values are defined:

|NPI|Value|
|---|---|
|Unknown 00000000||
|ISDN (E163/E164)|00000001|
|Data (X.121)|00000011|
|Telex (F.69)|00000100|
|Land Mobile (E.212) National 00001000 Private 00001001 ERMES 00001010|00000110|
|Internet (IP)|00001110|
|WAP Client Id (to be defined by WAP Forum) All other values reserved|00010010|

**Table 4-43 NPI Values**

SMPP V5.0  SMS Forum 113 of 166

***4.7.3 address_range*** The *address_range* parameter is used in the *bind_receiver* and *bind_transceiver* command to specify a set of SME addresses serviced by the ESME client. A single SME address may also be specified in the *address_range* parameter. UNIX Regular Expression notation should be used to specify a range of addresses. Messages addressed to any destination in this range shall be routed to the ESME.
**Note:** For IP addresses, it is only possible to specify a single IP address. A range of IP
 addresses are not allowed. IP version 6.0 is not currently supported in this version of the protocol.
**Note:** It is likely that the addr_range field is not supported or deliberately ignored on most
 Message Centres. The reason for this is that most carriers will not allow an ESME control the message routing as this can carry the risk of mis-routing mesages. In such circumstances, the ESME will be requested to set the field to NULL.

##### 4.7.3.1 UNIX Regular Expressions

Full explanations of UNIX regular expressions can be found in section 5 of the standard online UNIX manuals (man 5 regexp). Furthermore, many UNIX books explain regular expressions and the various syntax used. This section gives useful and applicable examples of regular expressions in the context of the SMPP usage of same.

SMPP uses a regular expression in the *bind_receiver* and *bind_transceiver* PDUs. The ESME uses this to provide routing criteria to the SMSC, namely, TON, NPI and routing_expr. The TON & NPI values are fixed values where the routing_expr itself is the regular expression.

- ^1234 The ‘^’ char is used to represent `beginning with`, therefore ^1234 is interpreted as MSISDNs beginning with 1234. This allows an ESME specify a specific set of numbers based on a a given prefix common to all.
- 5678$ The ‘$’ char is used to represent `ending with`, thus 5678$ will match any MSISDN ending with 5678.
- ^123456$ A combination of ‘^’ and ‘$’ at the beginning and end of a regular expression, is used to specify an absolute address, i.e the above expression will match MSISDNs beginning with and ending with 123456. The only value ever matched to this will in fact be ‘123456’ itself.
- [13579]$ values within [] denote a character class. The above expression will match MSISDNs ending with any of 1, 3, 5, 7 or 9. So this expression will match MSISDNs ending in an odd digit. If a ‘^’ character is placed inside the ‘[‘, then the match is based on any character not in the specified class; e.g [^13579]$ will match MSISDNs not ending with any of the specified digits.
***4.7.4 command_length*** This 4-octet integer represents the overall length of a PDU. This is described in detail in section 3.2.1.1
SMPP V5.0  SMS Forum 114 of 166

***4.7.5 command_id*** The complete set of SMPP Command IDs and their associated values are defined in the following table.

|Command ID|Value|
|---|---|
|bind_receiver|0x00000001|
|bind_transmitter|0x00000002|
|query_sm|0x00000003|
|submit_sm|0x00000004|
|deliver_sm|0x00000005|
|unbind|0x00000006|
|replace_sm|0x00000007|
|cancel_sm|0x00000008|
|bind_transceiver|0x00000009|
|outbind|0x0000000B|
|enquire_link|0x00000015|
|submit_multi|0x00000021|
|alert_notification|0x00000102|
|data_sm|0x00000103|
|broadcast_sm|0x00000111|
|query_broadcast_sm|0x00000112|
|cancel_broadcast_sm|0x00000113|
|generic_nack|0x80000000|
|bind_receiver_resp|0x80000001|
|bind_transmitter_resp|0x80000002|
|query_sm_resp|0x80000003|
|submit_sm_resp|0x80000004|
|deliver_sm_resp|0x80000005|
|unbind_resp|0x80000006|
|replace_sm_resp|0x80000007|
|cancel_sm_resp|0x80000008|
|bind_transceiver_resp|0x80000009|
|enquire_link_resp|0x80000015|
|submit_multi_resp|0x80000021|
|data_sm_resp|0x80000103|
|broadcast_sm_resp|0x80000111|
|query_broadcast_sm_resp|0x80000112|
|cancel_broadcast_sm_resp|0x80000113|
|Reserved for MC Vendor|0x00010200 - 0x000102FF|

##### 0x80010200 - 0x800102FF

SMPP V5.0  SMS Forum 115 of 166

|Command ID|Value||
|---|---|---|
|All other values reserved|||
|Table 4-44 4.7.6 command_status, error_status_code The command_status NULL in SMPP request messages. The SMPP Error status codes are returned by the MC in the SMPP message header and in the following table.|command_id a SMPP request. It is relevant only in the SMPP response message and should be set to error_status_code The complete set of SMPP Error Codes and their associated values are defined in the|Values field of a SMPP message response indicates the success or failure of command_status field of the field of a submit_multi_resp message.|
|Command Status Name|Value|Description|
|ESME_ROK 0x00000000 No Error.||Specified in a response PDU to indicate the success of the corresponding request PDU.|
|ESME_RINVMSGLEN|0x00000001|Message Length is invalid. short_message field or message_ payload TLV has an invalid length (usually too long for the given MC or underlying network technology).|
|ESME_RINVCMDLEN|0x00000002|Command Length is invalid. PDU length is considered invalid, either because the value is too short or too large for the given PDU.|
|ESME_RINVCMDID|0x00000003 ESME_RINVBNDSTS 0x00000004 Incorrect BIND Status for given com-|Invalid Command ID. Command ID is not recognised, either because the operation is not supported or unknown. mand. PDU has been sent in the wrong session state. E.g. sending a submit_sm without first establishing a Bound_TX session state.|
|ESME_RALYBND|0x00000005|ESME Already in Bound State. A bind request has been issued within a session that is already bound.|
|ESME_RINVPRTFLG|0x00000006 ESME_RINVREGDLVFLG 0x00000007 Invalid Registered Delivery Flag.|Invalid Priority Flag. Priority flag contains an illegal or unsupported value. Registered field contains an invalid setting.|

SMPP V5.0  SMS Forum 116 of 166

|Command Status Name|Value|Description|
|---|---|---|
|ESME_RSYSERR 0x00000008 System Error.||MC system error indicating that all or part of the MC is currently unavailable. This can be returned in any response PDU.|
|ESME_RINVSRCADR|0x0000000A ESME_RINVDSTADR 0x0000000B Invalid Destination Address.|Invalid Source Address. Source address of message is considered invalid. Usually this is because the field is either too long or contains invalid characters. Destination address of message is considered invalid. Usually this is because the field is either zero length, too long or contains invalid characters.|
|ESME_RINVMSGID ESME_RBINDFAIL 0x0000000D Bind Failed.|0x0000000C ESME_RINVPASWD 0x0000000E Invalid Password.|Message ID is invalid. Message ID specified in cancel_sm, query_sm or other operations is invalid. A generic failure scenario for a bind attempt. This may be due to a provisioning error, incorrect password or other reason. A MC will typically return this error for an invalid system_id, system_type, password or other attribute that may cause a bind failure. Password field in bind PDU is invalid. This is usually returned when the length is too short or too long. It is not supposed to be returned when the ESME has specified the incorrect password.|
|ESME_RINVSYSID|0x0000000F|Invalid System ID. The System ID field in bind PDU is invalid. This is usually returned when the length is too short or too long. It is not supposed to be returned when the ESME has specified the incorrect system id.|
|ESME_RCANCELFAIL|0x00000011|Cancel SM Failed. Generic failure error for cancel_sm operation.|
|ESME_RREPLACEFAIL|0x00000013|Replace SM Failed. Generic failure for replace_sm operation.|

SMPP V5.0  SMS Forum 117 of 166

|Command Status Name|Value|Description|
|---|---|---|
|ESME_RMSGQFUL|0x00000014|Message Queue Full. Used to indicate a resource error within the MC. This may be interpreted as the maximum number of messages addressed to a single destination or a global maximum of undelivered messages within the MC.|
|ESME_RINVSERTYP|0x00000015|Invalid Service Type. Service type is rejected either because it is not recognised by the MC or because its length is not within the defined range.|
|ESME_RINVNUMDESTS|0x00000033 ESME_RINVDLNAME 0x00000034 Invalid Distribution List name.|Invalid number of destinations. The number_of_dests field in the submit_multi PDU is invalid. The dl_name field specified in the submit_multi PDU is either invalid, or non-existent.|
|ESME_RINVDESTFLAG ESME_RINVESMCLASS 0x00000043 Invalid|0x00000040 ESME_RINVSUBREP 0x00000042 Submit w/replace functionality has been|Destination flag is invalid (submit_multi). The dest_flag field in the submit_multi PDU has been encoded with an invalid setting. requested where it is either unsupported or inappropriate for the particular MC. This can typically occur with submit_multi where the context of `replace if present` is often a best effort operation and MCs may not support the feature in submit_multi. Another reason for returning this error would be where the feature has been denied to an ESME. esm_class field data. The esm_class field has an unsupported setting.|
|ESME_RCNTSUBDL ESME_RSUBMITFAIL 0x00000045|0x00000044|Cannot Submit to Distribution List. Distribution lists are not supported, are denied or unavailable. submit_sm, data_sm or submit_multi failed. Generic failure error for submission operations.|
|ESME_RINVSRCTON|0x00000048|Invalid Source address TON. The source TON of the message is either invalid or unsupported.|

SMPP V5.0  SMS Forum 118 of 166

|Command Status Name|Value|Description|
|---|---|---|
|ESME_RINVSRCNPI ESME_RINVDSTTON 0x00000050 Invalid ESME_RINVSYSTYP 0x00000053 Invalid ESME_RINVREPFLAG 0x00000054 Invalid|0x00000049 ESME_RINVDSTNPI 0x00000051 Invalid Destination address NPI.|Invalid Source address NPI. The source NPI of the message is either invalid or unsupported. Destination address TON. The destination TON of the message is either invalid or unsupported. The destination NPI of the message is either invalid or unsupported. system_type field. The System type of bind PDU has an incorrect length or contains illegal characters. replace_if_present flag. The replace_if_present flag has been encoded with an invalid or unsupported setting.|
|ESME_RINVNUMMSGS|0x00000055|Invalid number of messages.|
|ESME_RTHROTTLED|0x00000058|Throttling error (ESME has exceeded allowed message limits). This type of error is usually returned where an ESME has exceeded a predefined messaging rate restriction applied by the operator.|
|ESME_RINVSCHED|0x00000061 ESME_RINVEXPIRY 0x00000062 Invalid message validity period (Expiry ESME_RINVDFTMSGID 0x00000063 Predefined Message ID is Invalid or|Invalid Scheduled Delivery Time. Scheduled delivery time is either the incorrect length or is invalid. time). Expiry time is either the incorrect length or is invalid. specified predefined message was not found. The default (pre-defined) message id is either invalid or refers to a non-existent pre-defined message.|
|ESME_RX_T_APPN|0x00000064|ESME Receiver Temporary App Error Code. Rx or Trx ESME is unable to process a delivery due to a temporary problem and is requesting that the message be retried at some future point.|

SMPP V5.0  SMS Forum 119 of 166

|Command Status Name|Value|Description|
|---|---|---|
|ESME_RX_P_APPN|0x00000065|ESME Receiver Permanent App Error Code. Rx or Trx ESME is unable to process a delivery due to a permanent problem relating to the given destination address and is requesting that the message and all other messages queued to the same destination should NOT be retried any further.|
|ESME_RX_R_APPN ESME_RQUERYFAIL 0x00000067|0x00000066 ESME_RINVTLVSTREAM 0x000000C0 Error in the optional part of the PDU|ESME Receiver Reject Message Error Code. Rx or Trx ESME is unable to process a delivery due to a problem relating to the given message and is requesting that the message is rejected and not retried. This does not affect other messages queued for the same ESME or destination address. query_sm request failed. Generic failure scenario for a query request. Body. Decoding of TLVs (Optional Parameters) has resulted in one of the following scenarios: • PDU decoding completed with 1- 3 octets of data remaining, indicating a corrupt PDU. • A TLV indicated a length that was not present in the remaining PDU data (e.g. a TLV specifying a length of 10 where only 6 octets of PDU data remain).|
|ESME_RTLVNOTALLWD|0x000000C1|TLV not allowed. A TLV has been used in an invalid context, either inappropriate or deliberately rejected by the operator.|
|ESME_RINVTLVLEN|0x000000C2|Invalid Parameter Length. A TLV has specified a length that is considered invalid.|
|ESME_RMISSINGTLV|0x000000C3|Expected TLV missing. A mandatory TLV such as the message_payload TLV within a data_sm PDU is missing.|
|ESME_RINVTLVVAL|0x000000C4|Invalid TLV Value. The data content of a TLV is invalid and cannot be decoded.|

SMPP V5.0  SMS Forum 120 of 166

##### Command Status Name Value Description

ESME_RDELIVERYFAILURE 0x000000FE Transaction Delivery Failure.

A *data_sm* or *submit_sm* operation issued in transaction mode has resulted in a failed delivery. ESME_RUNKNOWNERR 0x000000FF Unknown Error.

Some unexpected error has occurred. ESME_RSERTYPUNAUTH 0x00000100 ESME Not authorised to use specified *service_type.*

Specific *service_type* has been denied for use by the given ESME.

ESME_RPROHIBITED 0x00000101 ESME Prohibited from using specified operation.

The PDU request was recognised but is denied to the ESME.

##### ESME_RSERTYPUNAVAIL 0x00000102 Specified service_type is unavailable

Due to a service outage within the MC, a service is unavailable.

##### ESME_RSERTYPDENIED 0x00000103 Specified service_type is denied

Due to inappropriate message content wrt. the selected *service_type.*

##### ESME_RINVDCS 0x00000104 Invalid Data Coding Scheme

Specified DCS is invalid or MC does not support it.

ESME_RINVSRCADDRSUBUNIT 0x00000105 Source Address Sub unit is Invalid.

ESME_RINVDSTADDRSUBUNIT 0x00000106 Destination Address Sub unit is Invalid.

ESME_RINVBCASTFREQINT 0x00000107 Broadcast Frequency Interval is invalid.

Specified value is either invalid or not supported.

ESME_RINVBCASTALIAS_NAME 0x00000108 Broadcast Alias Name is invalid.

Specified value has an incorrect length or contains invalid/unsupported characters.

ESME_RINVBCASTAREAFMT 0x00000109 Broadcast Area Format is invalid.

Specified value violates protocol or is unsupported.

ESME_RINVNUMBCAST_AREAS 0x0000010A Number of Broadcast Areas is invalid.

Specified value violates protocol or is unsupported.

ESME_RINVBCASTCNTTYPE 0x0000010B Broadcast Content Type is invalid.

Specified value violates protocol or is unsupported.

ESME_RINVBCASTMSGCLASS 0x0000010C Broadcast Message Class is invalid.

Specified value violates protocol or is unsupported.

SMPP V5.0  SMS Forum 121 of 166

##### Value

|Command Status Name|Description|
|---|---|
|ESME_RBCASTFAIL 0x0000010D|broadcast_sm operation failed.|
|ESME_RBCASTQUERYFAIL 0x0000010E|query_broadcast_sm operation failed.|

##### ESME_RBCASTCANCELFAIL 0x0000010F cancel_broadcast_sm operation failed

ESME_RINVBCAST_REP 0x00000110 Number of Repeated Broadcasts is invalid.

Specified value violates protocol or is unsupported.

ESME_RINVBCASTSRVGRP 0x00000111 Broadcast Service Group is invalid.

Specified value violates protocol or is unsupported.

ESME_RINVBCASTCHANIND 0x00000112 Broadcast Channel Indicator is invalid.

Specified value violates protocol or is unsupported.

|||unsupported.|
|---|---|---|
|Reserved for MC vendor|0x00000400-|Reserved for MC vendor specific errors.|
|specific errors|0x000004FF||
|All other values reserved|||

**Table 4-45 *command_status* Values**

SMPP V5.0  SMS Forum 122 of 166

***4.7.7 data_coding*** The following values are defined for this field:

|data_coding Bits||Meaning||Notes|
|---|---|---|---|---|
|7 6 5 4 3 2 1 0|||||

|0 0 0 0 0 0 0 0|MC Specific (See footnote)|
|---|---|
|0 0 0 0 0 0 0 1|IA5 (CCITT T.50)/ASCII (ANSI X3.4)|
|0 0 0 0 0 0 1 0|Octet unspecified (8-bit binary)|
|0 0 0 0 0 0 1 1|Latin 1 (ISO-8859-1)|
|0 0 0 0 0 1 0 0|Octet unspecified (8-bit binary)|
|0 0 0 0 0 1 0 1|JIS (X 0208-1990)|
|0 0 0 0 0 1 1 0|Cyrillic (ISO-8859-5)|
|0 0 0 0 0 1 1 1|Latin/Hebrew (ISO-8859-8)|
|0 0 0 0 1 0 0 0|UCS2 (ISO/IEC-10646)|
|0 0 0 0 1 0 0 1|Pictogram Encoding|
|0 0 0 0 1 0 1 0|ISO-2022-JP (Music Codes)|
|0 0 0 0 1 0 1 1|Reserved|
|0 0 0 0 1 1 0 0|Reserved|
|0 0 0 0 1 1 0 1|Extended Kanji JIS (X 0212-1990)|
|0 0 0 0 1 1 1 0|KS C 5601|
|0 0 0 0 1 1 1 1........ 1 0 1 1 1 1 1 1|reserved|
|1 1 0 0 x x x x|GSM MWI control-see [GSM 03.38]|
|1 1 0 1 x x x x|GSM MWI control-see [GSM 03.38]|
|1 1 1 0 x x x x|Reserved GSM message class control-see|

7 1 1 1 1 x x x x [GSM 03.38]

**Table 4-46 *data_coding* Values**

3 This represents the default alphabet assumed by the MC. This coding scheme may differ from vendor to vendor or may vary according to location and network technology of MC. For portability of applications, it is strongly recommended to avoid using this setting. 4 In cases where a Data Coding Scheme is defined for TDMA and/ or CDMA but not defined for GSM, SMPP uses GSM 03.38 reserved values. 5 These coding schemes are common to GSM, TDMA and CDMA. The SMPP protocol allows ESME applications to use the same DCS value (i.e. the GSM 03.38 value) for all three technologies. 6 The *data_coding* parameter will evolve to specify Character code settings only. Thus the recommended way to specify GSM MWI control is by specifying the relevant settings in the TLVs *ms_msg_wait_facilities* and *ms_validity.* 7 The *data_coding* parameter will evolve to specify Character code settings only. Thus the recommended way to specify GSM message class control is by specifying the relevant setting in the TLV *dest_addr_subunit*.

SMPP V5.0  SMS Forum 123 of 166

***4.7.8 destination_addr*** Specifies the destination SME address. For mobile terminated messages, this is the directory number of the recipient MS. <u>Notes</u>

|An IP address is specified in `aaa.bbb.ccc.ddd` notation. IP version 6.0 is not supported.|A flag, which will identify whether destination address is a Distribution List (DL) name or SME|
|---|---|
|dest_flag Value 0x01 SME Address|Meaning|
|0x02|Distribution List Name|
|Table 4-47 defined by mutual agreement between the MC and the ESME. Specifies the address of an ESME address to which an|dest_flag Values The reference name for a distribution list provisioned on the MC. Distribution list names are alert_notification|

***4.7.9 dest_flag*** address.
***4.7.10 dl_name***
***4.7.11 esme_addr***
should be routed.

<u>Notes</u>

An IP address is specified in `aaa.bbb.ccc.ddd` notation. IP version 6.0 is not supported.

SMPP V5.0  SMS Forum 124 of 166

***4.7.12 esm_class*** The *esm_class* parameter is used to indicate special message attributes associated with the short message. The *esm_class* parameter is encoded as follows in the *submit_sm, submit_multi* and *data_sm* (ESME -> MC) PDUs:

||esm_class Bits|Meaning|
|---|---|---|
||7 6 5 4 3 2 1 0 x x x x x x 0 0 x x x x x x 0 1 x x x x x x 1 0|Default MC Mode (e.g. Store and Forward) Datagram mode Forward (i.e. Transaction) mode|
|(bits 1-0)|x x x x x x 1 1|Store and Forward mode (use to select Store and Forward mode if Default MC|
|Messaging Mode|x x 0 0 0 0 x x x x 0 0 0 1 x x|Mode is non Store and Forward) Default message Type (i.e. normal message) Short Message contains MC Delivery Receipt|
|(bits 2 and 5)|x x 1 0 0 0 x x|Short Message contains Intermediate Delivery|
|Message Type|x x 0 0 1 0 x x x x 0 1 0 0 x x|Notification Short Message contains Delivery Acknowledgement Short Message contains Manual/User Acknowledgement|
|(bits 5-2)|x x 0 1 1 0 x x|Short Message contains Conversation Abort (Korean|
|ANSI-41 Specific|0 0 x x x x x x 0 1 x x x x x x 1 0 x x x x x x|CDMA) No specific features selected UDH Indicator Set Reply Path (only relevant for GSM network)|
|(bits 7-6)|1 1 x x x x x x|Set UDHI and Reply Path (only relevant for GSM|
|GSM Specific||network)|

**Table 4-48 *esm_class* Bit Values**
 The default setting of the *esm_class* parameter is 0x00.

##### Notes

- If an ESME encodes GSM User Data Header information in the short message user data, it must set the UDHI flag in the *esm_class* field.
- If the MC delivers a short message that contains GSM User Data Header information encoded in the *short_message* or *message_payload* parameter, it must set the UDHI flag in the *esm_class* field.
- UDH encoded messages have all UDH-related information encoded directly into the message text. This includes the settings for fragmentation and port settings.
- For GSM networks, the concatenation related TLVs (*sar_msg_ref_num*, *sar_total_segments, sar_segment_seqnum*) or port addressing related TLVs
SMPP V5.0  SMS Forum 125 of 166

(*source_port, dest_port*) cannot be used in conjunction with encoded User Data Header in the *short_message* (user data) field. This means that the above listed TLVs cannot be used if the User Data Header Indicator flag is set.

***4.7.13 interface_version*** This parameter is used to indicate the version of the SMPP protocol. The following interface version values are defined:

|Interface Version|Value|
|---|---|
|Indicates that the ESME supports version 3.3 or earlier of the SMPP protocol.|0x00-0x33|
|Indicates that the ESME is supporting SMPP version 3.4|0x34|
|Indicates that the ESME is supporting SMPP version 5.0 All other values reserved|0x50|

**Table 4-49 *interface_version* Values**

***4.7.14 message_id*** The unique message identifier reference assigned by the MC to each submitted short message. It is an opaque value and is set according to MC implementation. It is returned by the MC in the *submit_sm_resp, submit_multi_resp* and *data_sm_resp* PDUs and may be used by the ESME in subsequent SMPP operations relating to the short message, e.g. the ESME can use the *query_sm* operation to query a previously submitted message using the MC *message_id* as the message handle***.*** The *message_id* is also returned in the *broadcast_sm_resp* and may be used by the ESME in subsequent SMPP broadcast operations relating to the short message, e.g. the ESME can use the *query_broadcast_sm* operation to query a previously submitted broadcast message using the MC *message_id* as the message handle.
SMPP V5.0  SMS Forum 126 of 166

***4.7.15 message_state*** The following is a list of allowable states for a short message. The MC returns the *message_state value* to the ESME as part of the *query_sm_resp* or *query_broadcast_sm_resp* PDU. Intermediate states are states that can change. Final states are states that represent an end of life state for a message. For example, a message in retry may return an ENROUTE state. At some point in the future, this message will either expire or be delivered. The state will then progress to EXPIRED or DELIVERED. Thus a message in ENROUTE state is said to be in an intermediate state. A message in DELIVERED or EXPIRED state cannot progress to another state. These states are therefore final states.

|Message State|Value|Type|Description|
|---|---|---|---|
||||SCHEDULED 0 Intermediate The message is scheduled. Delivery has not yet been initiated. A message submitted with a scheduled delivery time may return this state when queried. This value was added for V5.0 of SMPP and V3.4 and earlier MCs are likely to return ENROUTE for scheduled messages.|
|ENROUTE|1|Intermediate|The message is in enroute state. This is a general state used to describe a message as being active within the MC. The message may be in retry or dispatched to a mobile network for delivery to the mobile.|
|DELIVERED⁸|2|Final|Message is delivered to destination The message has been delivered to the destination. No further deliveries will occur.|
|EXPIRED|3|Final|Message validity period has expired. The message has failed to be delivered within its validity period and/or retry period. No further delivery attempts will be made.|
|DELETED|4|Final|Message has been deleted. The message has been cancelled or deleted from the MC. No further delivery attempts will take place.|
|UNDELIVERABLE|5|Final|Message is undeliverable. The message has encountered a delivery error and is deemed permanently undeliverable. No further delivery attempts will be made. Certain network or MC internal errors result in the permanent non-delivery of a message. Examples of such errors would be an unknown subscriber or network error that indicated that the given destination mobile was denied SMS service or could not support SMS.|

##### Not relevant for Cell Broadcast Service

SMPP V5.0  SMS Forum 127 of 166

|Message State|Value|Type|Description|
|---|---|---|---|
|ACCEPTED|6|Final|Message is in accepted state (i.e. has been manually read on behalf of the subscriber by customer service) This state is used to depict intervention on the MC side. Sometimes a malformed message can cause a mobile to power-off or experience problems. The result is that all messages to that mobile may remain queued until the problem message is removed or expires. In certain circumstances, a mobile network support service or administrator may manually accept a message to prevent further deliveries and allow other queued messages to be delivered.|
|UNKNOWN|7|N/A|Message is in invalid state The message state is unknown. This may be due to some internal MC problem which may be intermediate or a permanent. This state should never be returned. A MC experiencing difficulties that prevents it from returning a message state, would use this state.|
|REJECTED|8|Final|Message is in a rejected state The message has been rejected by a delivery interface. The reasons for this rejection are vendor and network specific. No further delivery attempts will be made|
|SKIPPED|9|Final|The message was accepted but not transmitted or broadcast on the network. A skipped message is one that was deliberately ignored according to vendor or network-specific rules. No further delivery attempts will be made.|

**Table 4-50 *message_state* Values**

SMPP V5.0  SMS Forum 128 of 166

***4.7.16 no_unsuccess*** The number of unsuccessful SME destinations to which delivery was attempted for a *submit_multi* operation.
***4.7.17 number_of_dests*** The *number_of_dests* parameter indicates the number of *dest_address* structures that are to follow in the *submit_multi* operation. A maximum of 255 destination address structures are allowed.
***4.7.18 password*** The *password* parameter is used by the MC to authenticate the identity of the binding ESME. The Service Provider may require ESME’s to provide a password when binding to the MC. This password is normally issued by the MC system administrator. The *password* parameter may also be used by the ESME to authenticate the identity of the binding MC (e.g. in the case of the *outbind* operation).
***4.7.19 priority_flag*** The *priority_flag* parameter allows the originating SME to assign a priority level to the short message:
**9** **GSM (SMS)**

|Priority|GSM (CBS)|ANSI-136|IS-95|ANSI-41|
|---|---|---|---|---|
|||||(CBS)|
|0 non-priority Normal||Bulk|Normal Normal||
|1 Priority|Immediate Broadcast|Normal Interactive Interactive|||
|2 Priority|High priority Urgent Urgent Urgent||||
|3 Priority|reserved|Very Urgent Emergency Emergency|||
|4 N/A|Priority|N/A N/A N/A|||

Background¹⁰

##### All other values reserved

**Table 4-51 *priority_flag* Values**

**Note:** For CDMA and TDMA, priority is a visual message urgency indicator, hence the

different wording above. GSM does not support presentation of priority on the mobile station.

***4.7.20 protocol_id*** <u>GSM</u> Set according to GSM 03.40 [GSM 03.40]

##### <u>ANSI-136 (TDMA)</u>

For mobile terminated messages, this field is not used and is therefore ignored by the MC. For ANSI-136 mobile originated messages, the MC should set this value to NULL.

9 For GSM mobile terminated, messages with priority greater than Level 0 are treated as priority when making a delivery attempt (i.e. a delivery attempt is made even when MWD is set in the HLR). For GSM Cell Broadcast service the category of the message can have priority background, see [GSM 03.41] Section 9.2.7.

SMPP V5.0  SMS Forum 129 of 166

##### <u>IS-95 (CDMA)</u>

For mobile terminated messages, this field is not used and is therefore ignored by the MC. For IS-95 mobile originated messages, the MC should set this value to NULL.

***4.7.21 registered_delivery*** The *registered_delivery* parameter is used to request a MC delivery receipt and/or SME originated acknowledgements. The following values are defined:

||registered_delivery Bits|Meaning|
|---|---|---|
||7 6 5 4 3 2 1 0 x x x x x x 0 0 x x x x x x 0 1 x x x x x x 1 0|No MC Delivery Receipt requested (default) MC Delivery Receipt requested where final delivery outcome is delivery success or failure MC Delivery Receipt requested where the final delivery outcome is delivery failure. This includes|
|(bits 1 and 0)||scenarios where the message was cancelled via the cancel_sm operation.|
|MC Delivery Receipt SME originated (bits 3 and 2)|x x x x x x 1 1 x x x x 0 0 x x x x x x 0 1 x x x x x x 1 0 x x|MC Delivery Receipt requested where the final delivery outcome is success No recipient SME acknowledgment requested (default) SME Delivery Acknowledgement requested SME Manual/User Acknowledgment requested|
|Acknowledgement|x x x x 1 1 x x x x x 0 x x x x|Both Delivery and Manual/User Acknowledgment requested No Intermediate notification requested (default)|
|(bit 4)||Intermediate notification requested|
|Intermediate Notification|x x x 1 x x x x|Support for Intermediate Notification Functionality is specific to the MC implementation and is beyond the scope of the SMPP Protocol Specification.|
|All Bits|Reserved||

Other

**Table 4-52 *registered_delivery* Values**

SMPP V5.0  SMS Forum 130 of 166

***4.7.22 replace_if_present_flag*** The *replace_if_present_flag* parameter is used to request the MC to replace a previously submitted message that is pending delivery. The MC will replace an existing message provided that the source address, destination address and *service_type* match the same fields in the new message.

|Value|Meaning|
|---|---|
|0 1 Replace|Don’t replace (default)|
|2 - 255|Reserved|

**Table 4-53 *replace_if_present* Values**
 ESME applications that use this MC messaging function should use the same *service_type* and set the *replace_if_present_flag* parameter consistently to `1` for all messages, including the first message. This ensures that the MC has at most one message pending per destination SME for a particular application (e.g. voice mail notification).

##### 4.7.23 scheduled_delivery_time, validity_period, final_date

***4.7.23.1 scheduled_delivery_time*** This parameter specifies the scheduled time at which the message delivery should be first attempted. It defines either the absolute date and time or relative time from the current MC time at which delivery of this message will be attempted by the MC. It can be specified in either absolute time format or relative time format.
***4.7.23.2 validity_period*** The *validity_period* parameter indicates the MC expiration time, after which the message should be discarded if not delivered to the destination. It can be defined in absolute time format or relative time format.
***4.7.23.3 final_date*** The *final_date* parameter indicates the completion time for a message. It can only be specified in absolute format. See section 4.7.23.4 for more details.
SMPP V5.0  SMS Forum 131 of 166

##### 4.7.23.4 Absolute Time Format

This is the default format used by SMPP. Scheduled delivery times, expiry times and message completion dates are specified in UTC format, including the quarter hour offset and direction symbol ‘+’ or ‘-’.

Absolute time is formatted as a 16-character string (encoded as a 17-octet C-octet String) `**YYMMDDhhmmsstnnp**` where:

##### Digits Meaning

‘YY’ last two digits of the year (00-99)

‘MM’ month (01-12)

‘DD’ day (01-31)

‘hh’ hour (00-23)

‘mm’ minute (00-59)

‘ss’ second (00-59)

‘t’ tenths of second (0-9)

‘nn’ Time difference in quarter hours between local time (as expressed in the first 13 octets) and UTC (Universal Time Constant) time (00-48).

‘p’ `+` Local time is in quarter hours advanced in relation to UTC time.

`-` Local time is in quarter hours retarded in relation to UTC time.

**Table 4-54 Absolute UTC Time Format**

##### 4.7.23.5 Relative Time Format

Relative Time can be indicated by setting the UTC orientation flag to ‘R’ instead of ‘+’ or ‘-’. In this form, the MC interprets the time format as the number of years, months, days, hours, minutes and seconds from the current MC time. Values for tenths of seconds ‘t’ and UTC offset ‘nn’ are ignored and should be set to ‘0’ and ‘00’ respectively.

Absolute time is formatted as a 16 character string (encoded as a 17-octet C-octet String) `**YYMMDDhhmmsstnnp**` where:

##### Digits Meaning

‘YY’ year (00-99)

‘MM’ month (01-12)

‘DD’ day (01-31)

‘hh’ hour (00-23)

‘mm’ minute (00-59)

‘ss’ second (00-59)

‘t’ Unused. Should be set to ‘0’

‘nn’ Unused. Should be set to ‘00’

‘p’ `R` Local time is relative to the current MC time.

**Table 4-55 Relative Time Format**

SMPP V5.0  SMS Forum 132 of 166

For example, the following time format ‘020610233429000R`:

would be interpreted as a relative period of 2 years, 6 months, 10 days, 23 hours, 34 minutes and 29 seconds from the current MC time. A MC operator may choose to impose a limit on relative time offsets, thus either rejecting a message that exceeds such a limit or reducing the offset to the maximum relative time allowed.

For example: a typical validity period might be 7 days and a typical scheduled delivery times might be 14 days from the submission time.

***4.7.24 sequence_number*** A sequence number allows a response PDU to be correlated with a request PDU. The associated SMPP response PDU must preserve this field. The allowed *sequence_number* range is from 0x00000001 to 0x7FFFFFFF. In the event of a session using the full range of values for the *sequence_number*, the ESME or MC should wrap around to 0x00000001. The value 0x00000000 is recommended for use when issuing a *generic_nack* where the original PDU was deemed completely invalid and its PDU header, was not used to derive a *sequence_number* for the response PDU. Detailed information on how PDU sequencing works is available in section 2.6.
***4.7.25 service_type*** The *service_type* parameter can be used to indicate the SMS Application service associated with the message. Specifying the *service_type* allows the ESME to:

- Avail of enhanced messaging services such as *replace_if_present* by service type (generic to all network types).
- Control the teleservice used on the air interface (e.g. ANSI-136/TDMA, IS- 95/CDMA).
MCs may implicitly associate a ‘replace if present’ function from the indicated *service_type* in a message submission operation, i.e., the MC will always replace an existing message pending delivery, that has the same originating and destination address as the submitted message. For example, a MC can ensure that a Voice Mail System using a *service_type* of `VMA` has at most one outstanding notification per destination MS by automatically invoking the `replace if present` function.

Note: In the case of Cell Broadcast Service replace functionality by service type is not

supported.

##### The following generic service_types are defined

|Service Type|Description|
|---|---|
|`` (NULL)|Default|
|`CMT`|Cellular Messaging|
|`CPT`|Cellular Paging|
|`VMN`|Voice Mail Notification|
|`VMA`|Voice Mail Alerting|
|`WAP` `USSD` Unstructured Supplementary|Wireless Application Protocol Services Data|
|`CBS`|Cell Broadcast Service|
|`GUTS`|Generic UDP Transport Service|

**Table 4-56 *service_type* Values**

All other values are carrier specific and are defined by mutual agreement between the MC Service Provider and the ESME application.

SMPP V5.0  SMS Forum 133 of 166

***4.7.26 short_message*** The *short_message* parameter contains the user data. A maximum of 255 octets can be sent. ESME’s should use the optional *message_payload* parameter in *submit_sm,* *submit_multi,* and *deliver_sm* to send larger user data sizes.
Note: The *short_message* field is designed to carry binary payloads. This is why it is not
 specified as a C-Octet String. The value of the *sm_length* field indicates the explicit number of octets that the *short_message* field contains. NULL terminator octets (for ASCII content) should not be used in this field but if a NULL octet is included with the *short_message* data, then this octet MUST be included in the *sm_length* field.
***4.7.27 sm_default_msg_id*** The *sm_default_msg_id* parameter specifies the MC index of a pre-defined (‘canned’) message.

##### sm_default_msg_id Value Meaning

0 unused

##### 1-255 Allowed values

**Table 4-57 *sm_default_msg_id* Values**

***4.7.28 sm_length*** The *sm_length* parameter specifies the length of the *short_message* parameter in octets. The *sm_length* field should be set to 0 in the *submit_sm, submit_multi,* and *deliver_sm* PDUs if the *message_payload* parameter is being used to send user data larger than 255 octets.

##### sm_length Value Meaning

0 no user data in short message field

1-255 allowed

**Table 4-58 *sm_length* Values**

***4.7.29 source_addr*** Specifies the address of the SME, which originated this message. An ESME, which is implemented as a single SME address, may set this field to NULL to allow the MC to default the source address of the submitted message. <u>Notes</u> An IP address is specified in `aaa.bbb.ccc.ddd` notation. IP version 6.0 is not supported.
***4.7.30 system_id*** The *system_id* parameter is used to identify an ESME or a MC at bind time. An ESME *system_id* identifies the ESME or ESME agent to the MC. The MC *system_id* provides an identification of the MC to the ESME.
**4.7.31 system_type** The *system_type* parameter is used to categorize the type of ESME that is binding to the MC. Examples include `VMS` (voice mail system) and `OTA` (over-the-air activation system). Specification of the *system_type* is optional-some MCs may not require ESME’s to provide this detail. In this case, the ESME can set the *system_typ*e to NULL. SMPP V5.0  SMS Forum 134 of 166

##### 4.8 PDU TLV Definitions

TLV fields may be optionally included in a SMPP message. TLVs must always appear at the end of a SMPP PDU. However, they may be included in any convenient order and need not be encoded in the order presented in this document.

For a particular SMPP PDU, the ESME or MC may include some, all or none of the defined TLVs as required for the particular application context. For example a paging system may in a SMPP *submit_sm* operation, include only the `call-back number` related TLVs.

##### 4.8.1 TLV Tag

The SMPP protocol defines the following Parameter Tag blocks:

##### TLV Tag Range Meaning

0x1400 - 0x3FFF Reserved for MC Vendor specific TLVs

All other values Reserved for use by SMPP (Ref. Table 4-60)

**Table 4-59 TLV Tag Value Ranges**

The SMPP supported TLVs and their associated Tag Values are listed as follows:

|Tag|Value|Wireless Network||
|---|---|---|---|
|||Technology||
|dest_addr_subunit|0x0005 GSM|||
|dest_network_type|0x0006 Generic|||
|dest_bearer_type|0x0007 Generic|||
|dest_telematics_id|0x0008 GSM|||
|source_addr_subunit|0x000D GSM|||
|source_network_type|0x000E Generic|||
|source_bearer_type|0x000F Generic|||
|source_telematics_id|0x0010 GSM|||
|qos_time_to_live|0x0017 Generic|||
|payload_type|0x0019 Generic|||
|additional_status_info_text|0x001D Generic|||
|receipted_message_id|0x001E Generic|||
|ms_msg_wait_facilities|0x0030 GSM|||
|privacy_indicator|0x0201 CDMA, TDMA|||
|source_subaddress|0x0202 CDMA, TDMA|||
|dest_subaddress|0x0203 CDMA, TDMA|||
|user_message_reference|0x0204 Generic|||
|user_response_code|0x0205 CDMA, TDMA|||
|source_port|0x020A Generic|||
|dest_port|0x020B Generic|||
|sar_msg_ref_num|0x020C Generic|||
|language_indicator|0x020D CDMA, TDMA|||
|sar_total_segments|0x020E Generic  SMS Forum|||

##### SMPP V5.0 135 of 166

|Tag|Value|Wireless Network|
|---|---|---|
|||Technology|
|sar_segment_seqnum|0x020F Generic||
|sc_interface_version|0x0210 Generic||
|callback_num_pres_ind|0x0302 TDMA||
|callback_num_atag|0x0303 TDMA||
|number_of_messages|0x0304 CDMA||
|callback_num|0x0381|CDMA, TDMA, GSM,|
|||iDEN|
|dpf_result|0x0420 Generic||
|set_dpf|0x0421 Generic||
|ms_availability_status|0x0422 Generic||
|network_error_code|0x0423 Generic||
|message_payload|0x0424 Generic||
|delivery_failure_reason|0x0425 Generic||
|more_messages_to_send|0x0426 GSM||
|message_state|0x0427 Generic||
|congestion_state|0x0428 Generic||
|ussd_service_op|0x0501 GSM (USSD)||
|broadcast_channel_indicator|0x0600 GSM||
|broadcast_content_type broadcast_content_type_info broadcast_message_class broadcast_rep_num|0x0601 0x0602 CDMA, TDMA 0x0603 GSM 0x0604 GSM|CDMA, TDMA, GSM|
|broadcast_frequency_interval|0x0605|CDMA, TDMA, GSM|
|broadcast_area_identifier|0x0606|CDMA, TDMA, GSM|
|broadcast_error_status broadcast_area_success|0x0607 0x0608 GSM|CDMA, TDMA, GSM|
|broadcast_end_time broadcast_service_group billing_identification source_network_id dest_network_id source_node_id dest_node_id|0x0609 0x060A CDMA, TDMA 0x060B Generic 0x060D Generic 0x060E Generic 0x060F Generic 0x0610 Generic|CDMA, TDMA, GSM|
|dest_addr_np_resolution|0x0611|CDMA, TDMA (US Only)|
|dest_addr_np_information|0x0612|CDMA, TDMA (US Only)|
|dest_addr_np_country display_time sms_signal|0x0613 0x1201 CDMA, TDMA 0x1203 TDMA  SMS Forum|CDMA, TDMA (US Only)|

##### SMPP V5.0 136 of 166

|Tag|Value|Wireless Network|
|---|---|---|
|||Technology|
|ms_validity|0x1204 CDMA, TDMA||
|alert_on_message_delivery|0x130C CDMA||
|its_reply_type|0x1380 CDMA||
|its_session_info|0x1383 CDMA||

**Table 4-60 TLV Tag Definitions**

##### 4.8.2 TLV Length

This is a 2-octet field used to specify the length of the Value field within a TLV. It does not include the TLV length and TLV Tag fields.

##### 4.8.3 TLV Value

This is a variable size field used to contain the TLV payload for each specific TLV.

##### 4.8.4 TLV Definitions

***4.8.4.1 additional_status_info_text*** The *additional_status_info_text* parameter gives an ASCII textual description of the meaning of a response PDU. It is to be used by an implementation to allow easy diagnosis of problems.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x001D|
|Length|2|Integer|Length of Value part in octets|
|Value|1 - 256|C-Octet String Free format text to allow implementations to||

supply the most useful information for problem diagnosis. Maximum length is 256 octets.

**Table 4-61 *additional_status_info_text* TLV**

***4.8.4.2 alert_on_message_delivery*** The *alert_on_message_delivery* parameter is set to instruct a MS to alert the user (in a MS implementation specific manner) when the short message arrives at the MS.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer 0x130C||
|Length|2|Integer Length of Value part in octets (= 0)||
|Value|0 or 1||If length of value part is 0, then the default setting is|

assumed.

0 = Use mobile default alert (Default) 1 = Use low-priority alert 2 = Use medium-priority alert 3 = Use high-priority alert values 4 to 255 are reserved

**Table 4-62 *alert_on_message_delivery* TLV**

SMPP V5.0  SMS Forum 137 of 166

***4.8.4.3 billing_identification***

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x060B|
|Length|2|Integer|Length of Value part in octets|

Value 1-1024 Octet String Bits 7......0
0XXXXXXX (Reserved) 1XXXXXXX (Vendor Specific)

The first octet represents the Billing Format tag and indicates the format of the billing information in the remaining octets.

The remaining octets contain the billing information.

**Table 4-63 *billing_identification* TLV**

##### 4.8.4.4 broadcast_area_identifier, failed_broadcast_area_identifier

The *broadcast_area_identifier* defines the Broadcast Area in terms of a geographical descriptor.

|Field|Size octets|Type|Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x0606|
|Length|2|Integer|Length of Value part in octets|
|Value|Var.|Octet String|Octet 1 is used to specify the area format. Ref.|

4.8.4.4.1 The remaining data is used to specify the broadcast area details.
**Table 4-64 *broadcast_area_identifier* TLV**

##### 4.8.4.4.1 Broadcast Area Format types

##### Format Format Size Octets Value Type Description Technology

##### Value

alias/ name 0x00 Var. Octet String This field allows Generic Max.100 specification of an area by name.

ellipsoid_ 0x01 Var. Octet String This field allows GSM arc Max.100 specification of an area as an ellipsoid arc.

Ref. [3GPP TS 23.032] Sections: 5.7, 7.3.7

polygon 0x02 Var. Octet String This field allows GSM Max.100 specification of an area as a polygon.

Ref. [3GPP TS 23.032] Sections: 5.4, 7.3.4

##### All other values reserved

**Table 4-65 Broadcast Area Format Types**

SMPP V5.0  SMS Forum 138 of 166

***4.8.4.5 broadcast_area_success*** The *broadcast_area_success* parameter is a success rate indicator, defined as the ratio of the number of BTSs who accepted the message and the total number of BTSs who should accept the message, for a particular *broadcast_area_identifier*.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x0608|
|Length|2|Integer|Length of Value part in octets|
|Value|1|Integer|0-100 = allowed range|

##### 255 = Information not available

##### values 101 to 254 are reserved

**Table 4-66 *broadcast_area_success* TLV**

**4.8.4.6 *broadcast_content_type_info*** The *broadcast_content_type_info* parameter contains additional information specific to the *broadcast_content_type*.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x0602|
|Length Value Var.|2|Integer Octet String The value is a free format Octet String.|Length of Value part in octets|

1-255

**Table 4-67 *broadcast_content_type_info* TLV**

***4.8.4.7 broadcast_channel_indicator*** The *broadcast_channel_indicator* parameter specifies the Cell Broadcast channel that should be used for broadcasting the message.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x0600|
|Length|2|Integer|Length of Value part in octets|
|Value|1|Integer|0 = Basic Broadcast Channel (Default)|

##### 1 = Extended Broadcast Channel

##### values 2 to 255 are reserved

**Table 4-68 *broadcast_channel_indicator* TLV**

***4.8.4.8 broadcast_content_type*** The *broadcast_content_type* parameter specifies the content_type of the message content.

|Field|Size|Type|Description|
|---|---|---|---|
||octets|||
|Parameter Tag|2|Integer|0x0601|
|Length Value 3 Octet|2|Integer String|Length of Value part in octets The first octet is a Type of Network tag indicating the network type.|

Valid Tag values are: 0 = Generic 1 = GSM [23041] 2 = TDMA [IS824][ANSI-41] 3 = CDMA [IS824][IS637]

SMPP V5.0  SMS Forum 139 of 166

##### Field Size Type Description octets

all other values reserved

Octets 2-3 contain the broadcast content type. The following values apply when this is set to 0 (Generic):

##### Encoding Content Type

<u>System Services</u>

|0x0000|Index|
|---|---|
|0x0001|Emergency Broadcasts|
|0x0002|IRDB Download|

News Services

0x0010 News Flashes

|0x0011|General News (Local)|
|---|---|
|0x0012|General News (Regional)|
|0x0013|General News (National)|
|0x0014|General News (International)|
|0x0015|Business/Financial News (Local)|
|0x0016|Business/Financial News (Regional)|
|0x0017|Business/Financial News (National)|
|0x0018|Business/Financial News (International)|
|0x0019|Sports News (Local)|
|0x001A|Sports News (Regional)|
|0x001B|Sports News (National)|
|0x001C|Sports News (International)|
|0x001D|Entertainment News (Local)|
|0x001E|Entertainment News (Regional)|
|0x001F|Entertainment News (National)|
|0x0020|Entertainment News (International) Subscriber Information Services|

0x0021 Medical/Health/Hospitals

0x0022 Doctors

0x0023 Pharmacy

0x0030 Local Traffic/Road Reports

0x0031 Long Distance Traffic/Road Reports

0x0032 Taxis

0x0033 Weather

0x0034 Local Airport Flight Schedules

0x0035 Restaurants

0x0036 Lodgings

0x0037 Retail Directory

0x0038 Advertisements

SMPP V5.0  SMS Forum 140 of 166

##### Field Size Type Description octets

0x0038 Advertisements

##### 0x0039 Stock Quotes

##### 0x0040 Employment Opportunities

##### 0x0041 Technology News

||Carrier Information Services|
|---|---|
|0x0070|District (Base Station Info)|
|0x0071 Network Information|Subscriber Care Services|
|0x0080 Operator Services||
|0x0081|Directory Enquiries (National)|
|0x0082 Directory Enquiries (International)||
|0x0083|Customer Care (National)|
|0x0084|Customer Care (International)|
|0x0085|Local Date/Time/Time Zone Multi Category Services|
|0x0100|Multi Category Services|
|Reserved|All other values|

**Table 4-69 *broadcast_content_type* TLV**

SMPP V5.0  SMS Forum 141 of 166

***4.8.4.9 broadcast_end_time*** The *broadcast_end_time* parameter indicates the date and time at which the broadcasting state of this message was set to terminated in the Message Centre.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x0609|
|Length Value 16 C-Octet|2|Integer String|Length of Value part in octets This field is encoded in absolute UTC format as specified in Section 4.7.23.4|

**Table 4-70 *broadcast_end_time* TLV**

***4.8.4.10 broadcast_error_status*** The *broadcast_error_status* parameter specifies the nature of the failure associated with a particular *broadcast_area_identifier* specified in a broadcast request.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x0607|
|Length|2|Integer|Length of Value part in octets|
|Value|4|Integer|This field in the broadcast_sm_resp|

indicates the nature of the failure associated with a particular *broadcast_area_identifier* specified in a broadcast request.

The value is one of the SMPP Error Code values as defined in Section 4.7.6

**Table 4-71 *broadcast_error_status* TLV**

SMPP V5.0  SMS Forum 142 of 166

##### 4.8.4.11 broadcast _frequency_interval

The *broadcast_frequency_interval* parameter specifies the frequency interval at which the broadcasts of a message should be repeated.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x0605|
|Length|2|Integer|Length of Value part in octets|
|Value|3|Octet String The value of this parameter is encoded in three||

Octets as follows:

Octet 1: specifies the Units of Time specified as follows:

Encoding Time Unit

0x00 As frequently as possible

0x08 seconds

0x09 minutes

0x0A hours

0x0B days

0x0C weeks

0x0D months

0x0E years

Reserved All other values

Octet 2 + Octet 3: specifies the number of the specified time units in an unsigned integral format.

**Table 4-72 *broadcast_frequency_interval* TLV**

Example: 0x09001F => every 31 (0x1F) minutes (0x09)

***4.8.4.12 broadcast_message_class*** The *broadcast_message_class* parameter is used to route messages when received by a mobile station to user-defined destinations or to Terminal Equipment.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x0603|
|Length|2|Integer|Length of Value part in octets|
|Value|1|Integer|0x00 = No Class Specified (default)|

0x02 = Class 1 (User Defined) 0x02 = Class 2 (User Defined) 0x03 = Class 3 (Terminal Equipment)

Reserved all other values

**Table 4-73 *broadcast_message_class* TLV**

SMPP V5.0  SMS Forum 143 of 166

**4.8.4.13 *broadcast_rep_num*** This field indicates the number of repeated broadcasts requested by the Submitter.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x0604|
|Length|2|Integer|Length of Value part in octets|
|Value|2|Integer|The value 0 has the following significance:|

If no ‘*validity_period*’ has been specified for a broadcast, then the broadcasts should be repeated indefinitely.

If a ‘*validity_period*’ and a ‘*broadcast_frequency_interval’* have been specified, then 0 in this field indicates that the *broadcast_rep_num* is implicit according to the settings of these parameters.

Where a broadcast priority (i.e. priority_flag setting) of 1 (Immediate Broadcast) has been requested, then the *broadcast_rep_num* parameter should not be supplied and be ignored if supplied.

**Table 4-74 *broadcast_rep_num* TLV**

SMPP V5.0  SMS Forum 144 of 166

***4.8.4.14 broadcast_service_group*** The *broadcast_service_group* parameter is used to specify special target groups for broadcast information.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x060A|
|Length|2|Integer|Length of Value part in octets|
|Value|1-255|Octet String The value is a free format Octet String||

**Table 4-75 *broadcast_service_group* TLV**

***4.8.4.15 callback_num*** The *callback_num* parameter associates a call back number with the message. In TDMA networks, it is possible to send and receive multiple call-back numbers to/from TDMA mobile stations.

##### Field <u>Size octets</u> Type Description

<u>Parameter Tag</u> 2 Integer 0x0381 Length 2 Integer Length of Value part in octets
Value Var Octet String Bits 7......0
4 - 19 0000000D (octet 1) 00000TTT (octet 2) 0000NNNN (octet 3) XXXXXXXX (octet 4) : : XXXXXXXX (octet N)

The originating SME can set a Call Back Number for the receiving Mobile Station. The first octet contains the Digit Mode Indicator.

Bit D=0 indicates that the Call Back Number is sent to the mobile as DTMF digits encoded in TBCD.

Bit D=1 indicates that the Call Back Number is sent to the mobile encoded as ASCII digits.

The 2nd octet contains the Type of Number (TON). Encoded as in section 4.7.1

The third octet contains the Numbering Plan Indicator (NPI). Encoded as specified in section

4.7.2 The remaining octets contain the Call Back Number digits encoded as ASCII characters
**Table 4-76 *callback_num* TLV**

SMPP V5.0  SMS Forum 145 of 166

***4.8.4.16 callback_num_atag*** The *callback_num_atag* parameter associates an alphanumeric display with the call back number.

##### Field <u>Size octets</u> Type Description

<u>Parameter Tag</u> 2 Integer 0x0303 Length 2 Integer Length of Value part in octets Value Var Octet Alphanumeric display tag for call back number max string
65 Bits 7......0
EEEEEEEE (octet 1) XXXXXXXX (octet 2) : : XXXXXXXX (octet N)

The first octet contains the encoding scheme of the Alpha Tag display characters. This field contains the same values as for Data Coding Scheme (see section

4.7.7). The following octets contain the display characters: There is one octet per display character for 7-bit and 8- bit encoding schemes. There are two octets per display character for 16-bit encoding schemes.
**Table 4-77 *callback_num_atag* TLV**

***4.8.4.17 callback_num_pres_ind***

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x0302|
|Length|2|Integer|Length of Value part in octets|
|Value 1 Bit mask|||Bits 7......0|

```
0000ppss
```

This parameter controls the presentation indication and screening of the CallBackNumber at the mobile station. If present, the callback_num parameter must also be present. The Presentation Indicator is encoded in bits 2 and 3 as follows: 00 = Presentation Allowed 01 = Presentation Restricted 10 = Number Not Available 11 = Reserved

The Screening Indicator is encoded in bits 0 and 1 as follows: 00 = User provided, not screened 01 = User provided, verified and passed 10 = User provided, verified and failed 11 = Network Provided.

**Table 4-78 *callback_num_pres_ind* TLV**

SMPP V5.0  SMS Forum 146 of 166

***4.8.4.18 congestion_state*** The *congestion_state* parameter is used to pass congestion status information between ESME and MC as a means of providing flow control and congestion avoidance capabilities to the sending peer. The TLV can be used in any SMPP operation response PDU as a means of passing congestion status from one peer to another. Typical uses of this would be in *submit_sm/submit_sm_resp* sequences where an ESME would drive a batch of submissions at a high rate and use continual tracking of the returned *congestion_state* values as a means of gauging the congestion. Reaction to a variation in *congestion_state* would involve increasing/decreasing the rate as required to maintain the balance in the Optimum range.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x0428|
|Length|2|Integer|Length of Value part in octets|
|Value|1|Integer|0 = Idle|

1-29 = Low Load 30-49 = Medium Load 50-79 = High Load 80-90 = Optimum Load 90-99 = Nearing Congestion 100 = Congested / Maximum Load

##### All other values reserved

**Table 4-79 *congestion_state* TLV**

***4.8.4.19 delivery_failure_reason*** The *delivery_failure_reason* parameter is used in the *data_sm_resp* operation to indicate the outcome of the message delivery attempt (only applicable for transaction message mode). If a delivery failure due to a network error is indicated, the ESME may check the *network_error_code* parameter (if present) for the actual network error code.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x0425|
|Length|2|Integer|Length of value part in octets|
|Value|1|Integer|0 = Destination unavailable|

1 = Destination Address Invalid (e.g. suspended, no SMS capability, etc.) 2 = Permanent network error 3 = Temporary network error values 4 to are 255 reserved

**Table 4-80 *delivery_failure_reason* TLV**

The *delivery_failure_reason* parameter is not included if the delivery attempt was successful.

SMPP V5.0  SMS Forum 147 of 166

***4.8.4.20 dest_addr_np_country*** The *dest_addr_np_country* TLV is used to carry E.164 information relating to the operator country code.

##### Field <u>Size octets</u> Type Description

<u>Parameter Tag</u> 2 Integer 0x0613 Length 2 Integer Length of Value part in octets Value 1-5 Integer Country code of the origination operator (E.164 Region Code)

**Table 4-81 *dest_addr_np_country* TLV**

***4.8.4.21 dest_addr_np_information*** The *dest_addr_np_information* TLV is used to carry number portability information.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x0612|
|Length|2|Integer|Length of Value part in octets|
|Value|10|Octet String CDMA & TDMA (North America):||

When the Number Portability parameters are used within the US, the information contained with the NP Information will be the Location Routing Number (LRN). A LRN is a 10-digit number, in the format NPA-NXX-XXXX, that uniquely identifies a switch or point of interconnection (POI). The NPA-NXX portion of the LRN is used to route calls to numbers that have been ported.

**Table 4-82 *dest_addr_np_information* TLV**

***4.8.4.22 dest_addr_np_resolution*** The *dest_addr_np_resolution* TLV is used to pass an indicator relating to a number portability query. If this TLV is omitted, the default value is assumed.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x0611|
|Length|2|Integer|Length of Value part in octets|
|Value|1|Integer|0 = query has not been performed (default)|

1 = query has been performed, number not ported 2 = query has been performed, number ported

Note: When set to 2, the *dest_addr_np_information*

and *dest_addr_np_country* TLVs must also be included.

**Table 4-83 *dest_addr_np_resolution* TLV**

SMPP V5.0  SMS Forum 148 of 166

***4.8.4.23 dest_addr_subunit*** The *dest_addr_subunit* parameter is used to route messages when received by a mobile station, for example to a smart card in the mobile station or to an external device connected to the mobile station.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x0005|
|Length|2|Integer|Length of Value part in octets|
|Value|1|Integer|0x00 = Unknown (default)|

0x01 = MS Display 0x02 = Mobile Equipment 0x03 = Smart Card 1 (expected to be SIM if a SIM exists in the MS) 0x04 = External Unit 1 5 to 255 = reserved

**Table 4-84 *dest_addr_subunit* TLV**

***4.8.4.24 dest_bearer_type*** The *dest_bearer_type* parameter is used to request the desired bearer for delivery of the message to the destination address. In the case that the receiving system (e.g. MC) does not support the indicated bearer type, it may treat this a failure and return a response PDU reporting a failure.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x0007|
|Length|2|Integer|Length of Value part in octets|
|Value|1|Integer|0x00 = Unknown|

0x01 = SMS 0x02 = Circuit Switched Data (CSD) 0x03 = Packet Data 0x04 = USSD 0x05 = CDPD 0x06 = DataTAC 0x07 = FLEX/ReFLEX 0x08 = Cell Broadcast (cell cast) 9 to 255 = reserved

**Table 4-85 *dest_bearer_type* TLV**

***4.8.4.25 dest_network_id*** The *dest_network_id* assigned to a wireless network operator or ESME operator is a unique address that may be derived and assigned by the node owner without establishing a central assignment and management authority. When this TLV is specified, it must be accompanied with a *dest_node_id* TLV Ref.4.8.4.27.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x060E|
|Length|2|Integer|Length of Value part in octets|
|Value 7-65 C-Octet|||See 4.8.4.56|

String

**Table 4-86 *dest_network_id* TLV**

SMPP V5.0  SMS Forum 149 of 166

***4.8.4.26 dest_network_type*** The *dest_network_type* parameter is used to indicate a network type associated with the destination address of a message. In the case that the receiving system (e.g. MC) does not support the indicated network type, it may treat this a failure and return a response PDU reporting a failure.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x0006|
|Length|2|Integer|Length of Value part in octets|
|Value|1|Integer|0x00 = Unknown|

0x01 = GSM 0x02 = ANSI-136/TDMA 0x03 = IS-95/CDMA 0x04 = PDC 0x05 = PHS 0x06 = iDEN 0x07 = AMPS 0x08 = Paging Network 9 to 255 = reserved

**Table 4-87 *dest_network_type* TLV**

***4.8.4.27 dest_node_id*** The *dest_node_id* is a unique number assigned within a single ESME or MC network and must uniquely identify a destination node within the context of the MC or ESME. The content of a *dest_node_id* is comprised of decimal digits and is at the discretion of the owning ESME or MC.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x0610|
|Length|2|Integer|Length of Value part in octets|
|Value 6 Octet String|||Sequence of 6 decimal digits|

(Decimal)

**Table 4-88 *dest_node_id* TLV**

***4.8.4.28 dest_subaddress*** The *dest_subaddress* parameter specifies a subaddress associated with the destination of the message.

##### Field <u>Size octets</u> Type Description

<u>Parameter Tag</u> 2 Integer 0x0203 Length 2 Integer Length of Value part in octets Value Var Octet String See 4.8.4.60 for parameter 2 - 23 encoding.

**Table 4-89 *dest_subaddress* TLV**

The *dest_subaddress* parameter is not supported in the SMPP ***submit_multi*** PDU.

SMPP V5.0  SMS Forum 150 of 166

***4.8.4.29 dest_telematics_id*** This parameter defines the telematic interworking to be used by the delivering system for the destination address. This is only useful when a specific *dest_bearer_type* parameter has also been specified, as the value is bearer dependent. In the case that the receiving system (e.g. MC) does not support the indicated telematic interworking, it may treat this a failure and return a response PDU reporting a failure.

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x0008|
|Length|2|Integer Length of Value part in octets|

Value 2 Integer GSM: Octet1 is used to represent the *protocol_id* field as used by GSM. See 4.7.20

##### Octet2 is reserved

**Table 4-90 *dest_telematics_id* TLV**

***4.8.4.30 dest_port*** The *dest_port* parameter is used to indicate the application port number associated with the destination address of the message.

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x020B|
|Length|2|Integer Length of value part in octets|
|Value|2|Integer All values allowed.|

**Table 4-91 *dest_port* TLV**

***4.8.4.31 display_time*** The *display_time* parameter is used to associate a display time of the short message on the MS.

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x1201|
|Length|2|Integer Length of value part in octets|
|Value|1|Integer 0 = Temporary|

1 = Default (default) 2 = Invoke <u>values 3 to 255 are reserved</u>

**Table 4-92 *display_time* TLV**

SMPP V5.0  SMS Forum 151 of 166

***4.8.4.32 dpf_result*** The *dpf_result* parameter is used to indicate if delivery pending flag (DPF) was set for a delivery failure of a short message. When used in conjunction with transaction mode, *dpf_result* can be returned in a *submit_sm_resp* or *data_sm_resp* PDU. Where store and forward or datagram modes are used in the original submission, *dpf_result* may be returned as part of a delivery receipt in the form of a deliver_sm or data_sm PDU. If the *dpf_result* parameter is not returned, then the ESME should assume that DPF is not set.

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x0420|
|Length|2|Integer Length of value part in octets|
|Value|1|Integer 0 = DPF not set|

1 = DPF set <u>Values 2 to 255 are reserved</u>

**Table 4-93 *dpf_result* TLV**

***4.8.4.33 its_reply_type*** The *its_reply_type* parameter is a required parameter for the CDMA Interactive Teleservice as defined by the Korean PCS carriers [KORITS]. It indicates and controls the MS user’s reply method to an SMS delivery message received from the ESME.

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x1380|
|Length|2|Integer Length of Value part in octets|
|Value|1|Integer 0 = Digit|

##### Description

1 = Number 2 = Telephone No. 3 = Password 4 = Character Line 5 = Menu 6 = Date 7 = Time 8 = Continue <u>Values 9 to 255 are reserved</u>

**Table 4-94 *its_reply_type* TLV**

SMPP V5.0  SMS Forum 152 of 166

***4.8.4.34 its_session_info*** The *its_session_info* parameter is a required parameter for the CDMA Interactive Teleservice as defined by the Korean PCS carriers [KORITS]. It contains control information for the interactive session between an MS and an ESME.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x1383|
|Length|2|Integer|Length of Value part in octets|
 Value 2 Octet String

```
Bits 7......0
SSSSSSSS (octet 1)
NNNNNNNE (octet 2)
```

Octet 1 contains the session number (0 - 255) encoded in binary. The session number remains constant for each session.

The sequence number of the dialogue unit (as assigned by the ESME) within the session is encoded in bits 7..1 of octet 2.

The End of Session Indicator indicates the message is the end of the conversation session and is encoded in bit 0 of octet 2 as follows: 0 = End of Session Indicator inactive. 1 = End of Session Indicator active.

**Table 4-95 *its_session_info* TLV**

***4.8.4.35 language_indicator*** The *language_indicator* parameter is used to indicate the language of the short message.

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x020D|
|Length|2|Integer Length of value part in octets|
|Value|1|Integer 0 = unspecified (default)|

1 = English 2 = French 3 = Spanish 4 = German 5 = Portuguese Refer to [CMT-136] for other values

**Table 4-96 *language_indicator* TLV**

SMPP V5.0  SMS Forum 153 of 166

***4.8.4.36 message_payload*** The *message_payload* parameter contains the user data. Its function is to provide an alternative means of carrying text lengths above the 255 octet limit of the *short_message* field. Applications, which need to send messages longer than 255 octets, should use the *message_payload* TLV. When used in the context of a *submit_*sm PDU, the *sm_length* field should be set to zero.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x0424|
|Length|2|Integer|Set to length of user data|
|Value|Variable|Octet String Short message user data. The maximum size is||

##### MC and network implementation specific

**Table 4-97 *message_payload* TLV**

***4.8.4.37 message_state*** The *message_state* TLV is used by the MC in the *deliver_sm* and *data_sm* PDUs to indicate to the ESME the final message state for a MC Delivery Receipt. The *message_state* TLV is also returned by the MC to the ESME as part of the *query_broadcast_sm_resp* PDU.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer 0x0427||
|Length|2|Integer Length of value part in octets||
|Value|1||Values as per section 4.7.15|

**Table 4-98 *message_state* TLV**

***4.8.4.38 more_messages_to_send*** The *more_messages_to_send* parameter is used by the ESME in the *submit_sm* and *data_sm* operations to indicate to the MC that there are further messages for the same destination SME. The MC may use this setting for network resource optimisation.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer 0x0426||
|Length|2|Integer Length of value part in octets||
|Value|1||0 = No more messages to follow|

1 = More messages to follow (default)

##### values 2 to 255 are reserved

**Table 4-99 *more_messages_to_send* TLV**

SMPP V5.0  SMS Forum 154 of 166

***4.8.4.39 ms_availability_status*** The *ms_availability_status* parameter is used in the *alert_notification* operation to indicate the availability state of the MS to the ESME. If the MC does not include the parameter in the *alert_notification* operation, the ESME should assume that the MS is in an `available` state.

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x0422|
|Length|2|Integer Length of value part in octets|
|Value|1|Integer 0 = Available (Default)|

1 = Denied (e.g. suspended, no SMS capability, etc.) 2 = Unavailable values 3 to 255 are reserved

**Table 4-100 *ms_availability_status* TLV**

***4.8.4.40 ms_msg_wait_facilities*** The *ms_msg_wait_facilities* parameter allows an indication to be provided to an MS that there are messages waiting for the subscriber on systems on the PLMN. The indication can be an icon on the MS screen or other MMI indication.

|The ms_msg_wait_facilities can|||also specify the type of message associated with the|
|---|---|---|---|
|message waiting indication.||||
|Field|Size octets Type||Description|
|Parameter Tag|2|Integer|0x0030|
|Length|2|Integer|Length of Value part in octets|
|Value 1 Bit mask|||Bits 7......0|

```
I00000TT
```

This parameter controls the indication and specifies the message type (of the message associated with the MWI) at the mobile station.

The Indicator is encoded in bit 7 as follows: 0 = Set Indication Inactive 1 = Set Indication Active

The Type of Message associated with the MWI is encoded in bits 0 and 1 as follows:

00 = Voicemail Message Waiting 01 = Fax Message Waiting 10 = Electronic Mail Message Waiting 11 = Other Message Waiting

**Table 4-101 *ms_msg_wait_facilities* TLV**

SMPP V5.0  SMS Forum 155 of 166

***4.8.4.41 ms_validity*** The *ms_validity* parameter is used to provide an MS with validity information associated with the received short message.

##### Field <u>Size octets</u> Type Description

<u>Parameter Tag</u> 2 Integer 0x1204 Length 2 Integer Length of value part in octets Value 1 or 4 Octet Octet 1: specifies validity behaviour String 0 = Store Indefinitely (default) 1 = Power Down 2 = Valid until Registration Area Changes

##### 3 = Display Only

4 = Relative time period (which is specified in the following 3 octets.)

##### values 5 to 255 are reserved

Octet 2-4 are optional and when specified, provide extended validity information

Octet 2: specifies the Units of Time

##### Encoding Time Unit

|00 00 00 00|seconds|
|---|---|
|00 00 00 01|minutes|
|00 00 00 10|hours|
|00 00 00 11|days|
|00 00 01 00|weeks|
|00 00 01 01|months|
|00 00 01 10|years|

##### All other values reserved

Octet3 + Octet 4: specifies the number of the specified time units in an unsigned integer format.

**Table 4-102 *ms_validity* TLV**

SMPP V5.0  SMS Forum 156 of 166

***4.8.4.42 network_error_code*** The *network_error_code* parameter is used to indicate the actual network error code for a delivery failure. The network error code is technology specific.

##### Field <u>Size octets</u> Type Description

<u>Parameter Tag</u> 2 Integer 0x0423 Length 2 Integer Length of value part in octets Value 3 Octet String Sub-field Size Type Network Type 1 Integer Error Code 2 Integer

The first octet indicates the network type. The following values are defined: 1 = ANSI 136 Access Denied Reason 2 = IS 95 Access Denied Reason 3 = GSM 4 = ANSI 136 Cause Code 5 = IS 95 Cause Code 6 = ANSI-41 Error 7 = SMPP Error 8 = Message Center Specific

All other values reserved. The remaining two octets specify the actual <u>network error code appropriate to the network type.</u>

**Table 4-103 *network_error_code* TLV**

***4.8.4.43 number_of_messages*** The *number_of_messages* parameter is used to indicate the number of messages stored in a mailbox.

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x0304|
|Length|2|Integer Length of Value part in octets|
|Value|1|Integer 0 to 99 = allowed values.|

values 100 to 255 are reserved

**Table 4-104 *number_of_messages* TLV**

SMPP V5.0  SMS Forum 157 of 166

***4.8.4.44 payload_type*** The *payload_type* parameter defines the higher layer PDU type contained in the message payload.

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x0019|
|Length|2|Integer Length of Value part in octets|
|Value|1|Integer 0 = Default.|

In the case of a WAP application, the default higher layer message type is a WDP message. See [15]

##### 1= WCMP message

Wireless Control Message Protocol formatted data. See [14] for details.

##### Values 2 to 255 are reserved

**Table 4-105 *payload_type* TLV**

***4.8.4.45 privacy_indicator*** The *privacy_indicator* indicates the privacy level of the message.

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x0201|
|Length|2|Integer Length of value part in octets|
|Value|1|Integer 0 = Privacy Level 0 (Not Restricted)|

(default) 1 = Privacy Level 1 (Restricted) 2 = Privacy Level 2 (Confidential) 3 = Privacy Level 3 (Secret) Values 4 to 255 are reserved

**Table 4-106 *privacy_indicator* TLV**

SMPP V5.0  SMS Forum 158 of 166

***4.8.4.46 qos_time_to_live*** This parameter defines the number of seconds which the sender requests the MC to keep the message if undelivered before it is deemed expired. If the parameter is not present, the MC may apply a default value.

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x0017|
|Length|2|Integer Length of Value part in octets|
|Value|4|Integer Number of seconds for message to be retained by the|

receiving system.

**Table 4-107 *qos_time_to_live* TLV**

***4.8.4.47 receipted_message_id*** The *receipted_message_id* parameter indicates the ID of the message being receipted in a MC Delivery Receipt. This is the opaque MC message identifier that was returned in the *message_id* parameter of the SMPP response PDU that acknowledged the submission of the original message.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x001E|
|Length|2|Integer|Length of Value part in octets|
|Value|1 - 65|C-Octet String MC handle of the message being receipted.||

**Table 4-108 *receipted_message_id* TLV**

***4.8.4.48 sar_msg_ref_num*** The *sar_msg_ref_num* parameter is used to indicate the reference number for a particular concatenated short message. The concatenation related parameters are *sar_msg_ref_num*, *sar_total_segments* and *sar_segment_seqnum*. Where these are present the other parameters of the message should remain unchanged for each short message fragment which forms part of a mobile terminated concatenated short message, with the exception of those parameters for which it makes sense to change them (such as the user data in the *short_message* parameter).

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x020C|
|Length|2|Integer Length of value part in octets|
|Value|2|Integer This parameter shall contain an originator generated|

reference number so that a segmented short message may be reassembled into a single original message. This allows the parallel transmission of several segmented messages. This reference number shall remain constant for every segment which makes up a particular concatenated short message. When present, the PDU must also contain the *sar_total_segments* and *sar_segment_seqnum* <u>parameters. Otherwise this parameter shall be ignored.</u>

**Table 4-109 *sar_msg_ref_num* TLV**

SMPP V5.0  SMS Forum 159 of 166

***4.8.4.49 sar_segment_seqnum*** The *sar_segment_seqnum* parameter is used to indicate the sequence number of a particular short message within the concatenated short message.

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x020F|
|Length|2|Integer Length of value part in octets|
|Value|1|Integer This octet shall contain a value in the range 1 to 255|

indicating the sequence number of a particular message within the concatenated short message. The value shall start at 1 and increment by one for every message sent within the concatenated short message. When present, the PDU must also contain the *sar_total_segments* and *sar_msg_ref_num* parameters. Otherwise this parameter shall be ignored.

**Table 4-110 *sar_segment_seqnum* TLV**

***4.8.4.50 sar_total_segments*** The *sar_total_segments* parameter is used to indicate the total number of short messages within the concatenated short message.

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x020E|
|Length|2|Integer Length of value part in octets|
|Value|1|Integer This parameter shall contain a value in the range 1 to|

##### Description

255 indicating the total number of fragments within the concatenated short message. The value shall start at 1 and remain constant for every short message, which makes up the concatenated short message. When present, the PDU must also contain the *sar_msg_ref_num* and *sar_segment_seqnum* <u>parameters. Otherwise this parameter shall be ignored.</u>

**Table 4-111 *sar_total_segments* TLV**

***4.8.4.51 sc_interface_version*** The *sc_interface_version* parameter is used to indicate the SMPP version supported by the MC. It is returned in the bind response PDUs.

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x0210|
|Length|2|Integer Length of value part in octets|
|Value|1|Integer values as per 4.7.13|

(*interface_version*)

**Table 4-112 *sc_interface_version* TLV**

SMPP V5.0  SMS Forum 160 of 166

***4.8.4.52 set_dpf*** An ESME may use the *set_dpf* parameter to request the setting of a delivery pending flag (DPF) for certain delivery failure scenarios, such as MS unavailability (as indicated by the HLR). The MC should respond to such a request with an *alert_notification* PDU when it detects that the destination MS has become available. The delivery failure scenarios under which DPF is set is MC implementation and network implementation specific. If a delivery pending flag is set by the MC or network (e.g. HLR), then the MC should indicate this to the ESME in the *submit_sm_resp or data_sm_resp* PDU via the *dpf_result* parameter. It may also use a delivery receipt to relay this information and as a result may use a *deliver_sm* or *data_sm* PDU to carry the *dpf_result.* For more information see 4.8.4.32

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x0421|
|Length|2|Integer length of value part in octets|
|Value|1|Integer 0 = Setting of DPF for delivery failure to MS not|

requested

1 = Setting of DPF for delivery failure requested (default)

##### values 2 to 255 are reserved

**Table 4-113 *set_dpf* TLV**

***4.8.4.53 sms_signal*** The *sms_signal* parameter is used to provide a TDMA MS with alert tone information associated with the received short message.

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x1203|
|Length|2|Integer Length of Value part in octets|
|Value|2|Integer Encoded as per [CMT-136]|

**Table 4-114 *sms_signal* TLV**

***4.8.4.54 source_addr_subunit*** The *source_addr_subunit* parameter is used to indicate where a message originated in the mobile station, for example a smart card in the mobile station or an external device connected to the mobile station.

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x000D|
|Length|2|Integer Length of Value part in octets|

##### Value 1 Integer See 4.8.4.23

**Table 4-115 *source_addr_subunit* TLV**

SMPP V5.0  SMS Forum 161 of 166

***4.8.4.55 source_bearer_type*** The *source_bearer_type* parameter indicates the wireless bearer over which the message originated.

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x000F|
|Length|2|Integer Length of Value part in octets|

##### Value 1 Integer See 4.8.4.24

**Table 4-116 *source_bearer_type* TLV**

SMPP V5.0  SMS Forum 162 of 166

***4.8.4.56 source_network_id*** The *source_network_id* assigned to a wireless network operator or ESME operator is a unique address that may be derived and assigned by the node owner without establishing a central assignment and management authority. When this TLV is specified, it must be accompanied with a *source_node_id* TLV Ref. 4.8.4.58.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x060D|
|Length Value 7-65 C-Octet|2|Integer String|Length of Value part in octets For GSM Networks: 1 + MCC+ MNC; (1cccnn) Ref. [1]|

For TDMA or CDMA Networks: 2 + MCC+ SID; (2cccsssss) Ref. [12]

For ESME Operators: 3 + MCC + address type ind. + unique address

The following address type indicators are defined: Type Value Unique Address IP Address 1 Unique address derived from IPv4 or IPv6 IP address of resolved domain name of ESME operator.

For example, an ESME operator has a domain name of smpp.esme.com. A DNS lookup resolves smpp.esme.com to

141.204.178.86, the unique address is derived as 141205178086.
Note: 1 or 2-digit sub-
 domains are expanded into 3-digit numbers with leading zeros.
Alphanumeric 2 Alphanumeric domain name of ESME operator (for example smpp.esme.com)

E.164 3 E164 address
including country code

X.212 4 X.212 address of
ESME operator

**Table 4-117 *source_network_id* TLV**

SMPP V5.0  SMS Forum 163 of 166

***4.8.4.57 source_network_type*** The *source_network_type* parameter is used to indicate the network type associated with the device that originated the message.

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x000E|
|Length|2|Integer Length of Value part in octets|

##### Value 1 Integer See 4.8.4.26

**Table 4-118 *source_network_type* TLV**

***4.8.4.58 source_node_id*** The *source_node_id* is a unique number assigned within a single ESME or MC network and must uniquely identify an originating node within the context of the MC or ESME. The content of a *source_node_id* is comprised of decimal digits and is at the discretion of the owning ESME or MC.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x060F|
|Length|2|Integer|Length of Value part in octets|
|Value 6 Octet String|||Sequence of 6 decimal digits|

(Decimal)

**Table 4-119 *source_node_id* TLV**

***4.8.4.59 source_port*** The *source_port* parameter is used to indicate the application port number associated with the source address of the message.

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x020A|
|Length|2|Integer Length of value part in octets|
|Value|2|Integer All values allowed.|

**Table 4-120 *source_port* TLV**

SMPP V5.0  SMS Forum 164 of 166

***4.8.4.60 source_subaddress*** The *source_subaddress* parameter specifies a subaddress associated with the originator of the message.

##### Field <u>Size octets</u> Type Description

<u>Parameter Tag</u> 2 Integer 0x0202 Length 2 Integer Length of Value part in octets Value Var Octet String The first octet of the data field is a Type of 2 - 23 Subaddress tag and indicates the type of Sub-addressing information included, and implies the type and length of subaddressing information which can accompany this tag value in the data field.

##### Valid Tag values are

00000001 - Reserved 00000010 - Reserved 10000000 - NSAP (Even) [ITUT X.213] 10001000 - NSAP (Odd) [ITUT X.213] 10100000 - User Specified All other values Reserved

##### The remaining octets contain the subaddress

A NSAP address shall be encoded using the preferred binary encoding specified in [ITUT X.213]. In this case the subaddress field contains the Authority and Format Identifier.

A User Specified subaddress is encoded according to user specification, subject to a maximum of 22 octets.

**Table 4-121 *source_subaddress* TLV**

***4.8.4.61 source_telematics_id*** The *source_telematics_id* parameter indicates the type of telematics interface over which the message originated.

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x0010|
|Length|2|Integer Length of Value part in octets|

##### Value 1 Integer See 4.8.4.29

**Table 4-122 *source_telematics_id* TLV**

SMPP V5.0  SMS Forum 165 of 166

***4.8.4.62 user_message_reference*** A reference assigned by the originating SME to the short message. Depending on the destination network technology, this field may be passed directly to the mobile device. The *user_message_reference* TLV is also applicable in ancillary broadcast operations as a means of identifying a previously submitted message. In such cases, the *user_message_reference* can be used to substitute an actual *message_id* or may be used in conjunction with a *message_id.*

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x0204|
|Length|2|Integer Length of value part in octets|
|Value|2|Integer All values allowed.|

**Table 4-123 *user_message_reference* TLV**

***4.8.4.63 user_response_code*** A response code set by the user in a User Acknowledgement/Reply message. The response codes are application specific.

##### Description

|Field|Size octets Type||
|---|---|---|
|Parameter Tag|2|Integer 0x0205|
|Length|2|Integer Length of value part in octets|
|Value|1|Integer 0 to 255 (IS-95 CDMA)|

##### 0 to 15 (CMT-136 TDMA)

**Table 4-124 *user_response_code* TLV**

***4.8.4.64 ussd_service_op*** The *ussd_service_op* parameter is required to define the USSD service operation when SMPP is being used as an interface to a (GSM) USSD system.

|Field|Size octets Type||Description|
|---|---|---|---|
|Parameter Tag|2|Integer|0x0501|
|Length|2|Integer|Length of Value part in octets|
|Value|1|Integer|0 = PSSD indication|

1 = PSSR indication 2 = USSR request 3 = USSN request 4 to 15 = reserved

16 = PSSD response 17 = PSSR response 18 = USSR confirm 19 = USSN confirm

20 to 31 = reserved 32 to 255 = reserved for vendor specific USSD operations

**Table 4-125 *ussd_service_op* TLV**

SMPP V5.0  SMS Forum 166 of 166
