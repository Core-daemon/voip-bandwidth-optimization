# SIP and IAX2 Interworking

## Protocol Conversion Across a Bandwidth-Constrained VoIP Network

**Author:** Mohammad Sorower Jahan  
**Organisation:** Karnaphuli Online Limited (KOL)  
**Approximate project period:** 2010  
**Primary technologies:** SIP, IAX2, RTP, G.729, Asterisk and Linux

---

## 1. Purpose

This document explains how SIP and IAX2 were used together in the VoIP bandwidth optimisation architecture.

The existing telecommunications environment already used SIP.

Rather than replacing SIP throughout the network, I used two Asterisk systems to create an intermediate IAX2 transport section.

The first Asterisk server accepted calls from the existing Softswitch through SIP.

The call was then carried between the two Asterisk servers using IAX2.

At the remote side, the second Asterisk server converted the call back into SIP and passed it to the destination SIP gateway.

The basic call path was:

```text
Softswitch
    |
    | SIP / G.729
    v
Asterisk 1
Public IP
    |
    | IAX2 trunk / G.729
    v
Asterisk 2
Private IP behind NAT
    |
    | SIP / G.729
    v
SIP Gateway
```

This allowed the constrained part of the network to use IAX2 while preserving SIP compatibility at both ends.

---

## 2. Why Two Asterisk Servers Were Used

The two Asterisk servers acted as controlled protocol boundaries.

### Asterisk 1

The first Asterisk server was connected to the Softswitch side.

Its role was to:

- receive calls from the Softswitch using SIP
- evaluate the dialled destination
- route selected calls towards the IAX2 trunk
- preserve G.729 where possible
- send the call towards the remote Asterisk server

This server had a public IP address.

### Asterisk 2

The second Asterisk server was located at the remote side of the transport path.

It used a private IP address behind NAT.

Its role was to:

- receive calls through the IAX2 trunk
- process the destination number
- convert the call back into the SIP environment
- send the call towards the SIP gateway
- maintain the G.729 codec through the call path

Together, the two Asterisk systems formed the protocol-interworking layer.

---

## 3. End-to-End Call Flow

The complete logical call path was:

```text
+----------------------+
|      Softswitch      |
|                      |
|      SIP / G.729     |
+----------+-----------+
           |
           | SIP
           v
+----------------------+
|      Asterisk 1      |
|                      |
|      Public IP       |
|                      |
|   SIP -> IAX2        |
+----------+-----------+
           |
           | IAX2 trunk
           | G.729
           |
           v
+----------------------+
|      Asterisk 2      |
|                      |
| Private IP behind NAT|
|                      |
|   IAX2 -> SIP        |
+----------+-----------+
           |
           | SIP
           v
+----------------------+
|     SIP Gateway      |
|                      |
|      SIP / G.729     |
+----------------------+
```

The external systems did not need to understand the internal IAX2 transport design.

From the Softswitch side, the call entered through SIP.

From the destination side, the call also left through SIP.

The protocol change existed only between the two intermediate Asterisk systems.

---

## 4. SIP Call Leg

The Softswitch sent the call towards Asterisk 1 using SIP.

The SIP side of the configuration was handled through Asterisk's SIP configuration.

The relevant configuration areas included:

`/etc/asterisk/sip.conf`

and:

`/etc/asterisk/extensions.conf`

The SIP configuration defined how Asterisk communicated with the surrounding telecommunications systems.

The dialplan determined which calls should be sent into the IAX2 transport path.

---

## 5. IAX2 Call Leg

After receiving the SIP call, Asterisk 1 created the corresponding IAX2 call towards Asterisk 2.

The IAX2 relationship between the servers was configured as a trunk.

The main configuration file used for this part of the system was:

`/etc/asterisk/iax.conf`

The call therefore changed from:

```text
SIP
```

to:

```text
IAX2
```

at Asterisk 1.

At Asterisk 2, the reverse process occurred:

```text
IAX2
```

to:

```text
SIP
```

This was the central interworking function of the design.

---

## 6. IAX2 Trunk

The two Asterisk servers were connected using an IAX2 trunk.

The purpose of the trunk was to carry multiple VoIP calls across the same network path.

This was particularly useful because the project was designed for an environment where bandwidth availability was limited and large numbers of simultaneous calls could be present.

The transport design therefore looked like:

```text
Asterisk 1
     |
     |
     | Multiple VoIP sessions
     | transported through IAX2
     |
     v
Asterisk 2
```

During the original engineering work, the architecture was tested with more than 100 simultaneous calls.

The exact maximum concurrency value from the original tests has not been retained.

---

## 7. Public and Private Network Arrangement

The two Asterisk servers were not located in an identical network environment.

### Asterisk 1

Asterisk 1 had a public IP address.

It provided the externally reachable side of the IAX2 connection.

### Asterisk 2

Asterisk 2 had a private IP address and operated behind NAT.

The resulting arrangement was approximately:

```text
                       Internet / IP Network
                               |
                               |
                     Public IP address
                               |
                               v
                        +-------------+
                        | Asterisk 1  |
                        +-------------+
                               |
                               |
                            IAX2
                               |
                               |
                         NAT boundary
                               |
                               v
                        +-------------+
                        | Asterisk 2  |
                        | Private IP  |
                        +-------------+
                               |
                               |
                              SIP
                               |
                               v
                         SIP Gateway
```

This network arrangement was one of the practical considerations when establishing communication between the two systems.

---

## 8. NAT Considerations

The remote Asterisk server operated behind NAT.

This meant that the VoIP transport design had to account for the difference between the server's private address and the externally reachable network path.

IAX2 was useful in this type of architecture because signalling and media are handled within the IAX2 communication relationship rather than using the separate SIP signalling and RTP media arrangement normally associated with SIP calls.

This simplified the intermediate transport path in the project environment.

The exact historical firewall and NAT rules from the 2010 implementation are no longer available.

For that reason, this repository documents the architecture without claiming exact historical NAT rule values.

---

## 9. Codec Handling

G.729 was used throughout the tested call path.

The intended codec path was:

```text
Softswitch
   |
   | G.729
   v
Asterisk 1
   |
   | G.729
   v
Asterisk 2
   |
   | G.729
   v
SIP Gateway
```

Using the same codec throughout the path avoided introducing an intentional codec conversion stage as part of the bandwidth optimisation design.

This was important because transcoding could introduce:

- additional CPU utilisation
- additional latency
- codec licensing considerations
- changes in voice quality

The project therefore kept G.729 through the relevant call legs.

---

## 10. SIP Configuration

The SIP portions of the architecture were configured primarily through:

`/etc/asterisk/sip.conf`

This configuration handled the relationships between:

- the Softswitch and Asterisk 1
- Asterisk 2 and the SIP gateway

Typical SIP configuration areas included:

- peer definitions
- IP addresses
- authentication where required
- codec permissions
- call contexts
- NAT-related options
- SIP transport settings

Exact historical credentials and addresses are not reproduced in this repository.

---

## 11. IAX2 Configuration

The IAX2 connection was configured through:

`/etc/asterisk/iax.conf`

The file defined the relationship between the two Asterisk systems.

The configuration included the information required for the two systems to establish the IAX2 trunk and carry calls between them.

The project used G.729 across this transport section.

The exact original `iax.conf` file is no longer available.

Any configuration examples added to this repository in the future should therefore be clearly identified as reconstructed examples rather than original 2010 configuration files.

---

## 12. Dialplan Configuration

Call routing between SIP and IAX2 was controlled through:

`/etc/asterisk/extensions.conf`

The dialplan provided the logic connecting the different protocol legs.

Conceptually, the first side performed:

```text
Incoming SIP call
       |
       v
Destination analysis
       |
       v
Dial through IAX2 trunk
```

The second side performed:

```text
Incoming IAX2 call
       |
       v
Destination analysis
       |
       v
Dial through SIP gateway
```

The dialplan therefore provided the link between the two protocol environments.

---

## 13. Outbound Processing

A typical call beginning at the Softswitch followed this sequence.

### Step 1

The Softswitch sent a SIP call to Asterisk 1.

### Step 2

Asterisk 1 received the call through its configured SIP peer or trunk.

### Step 3

The Asterisk dialplan evaluated the destination.

### Step 4

The call was sent through the IAX2 trunk towards Asterisk 2.

### Step 5

Asterisk 2 received the IAX2 call.

### Step 6

The remote dialplan selected the appropriate SIP gateway route.

### Step 7

Asterisk 2 originated a SIP call towards the SIP gateway.

The resulting path was:

```text
Softswitch
    |
   SIP
    |
    v
Asterisk 1
    |
   IAX2
    |
    v
Asterisk 2
    |
   SIP
    |
    v
SIP Gateway
```

---

## 14. Reverse-Direction Calling

The architecture was designed to support communication in both directions where the surrounding routing configuration permitted it.

The reverse logical path was:

```text
SIP Gateway
    |
   SIP
    |
    v
Asterisk 2
    |
   IAX2
    |
    v
Asterisk 1
    |
   SIP
    |
    v
Softswitch
```

The same interworking principle applied in either direction.

Each Asterisk system acted as the protocol boundary for its side of the IAX2 transport link.

---

## 15. Media Transport

On the SIP call legs, SIP was used for call signalling while the corresponding media followed the negotiated RTP path.

Across the intermediate section, IAX2 carried the VoIP communication between the two Asterisk systems.

The logical transport arrangement was therefore:

```text
SIP signalling + RTP media
          |
          v
      Asterisk 1
          |
          | IAX2
          v
      Asterisk 2
          |
          v
SIP signalling + RTP media
```

This distinction is important because the bandwidth optimisation applied to the middle section rather than changing the external SIP interfaces.

---

## 16. Why SIP Was Kept at the Edges

The surrounding telecommunications infrastructure already supported SIP.

Changing all connected systems to another protocol would have increased the scope and complexity of the project.

Instead, the design kept:

```text
SIP at the external boundaries
```

and used:

```text
IAX2 internally
```

This allowed the bandwidth optimisation to be introduced without requiring changes to every connected gateway or Softswitch.

---

## 17. Why IAX2 Was Used in the Middle

The constrained network link was the part of the system where bandwidth efficiency mattered most.

The project therefore concentrated the optimisation effort on that section.

The IAX2 transport arrangement allowed the two Asterisk systems to carry calls across the constrained path while the surrounding network remained SIP-based.

During the historical testing, this arrangement was observed to use substantially less bandwidth than the original SIP/RTP transport path under the project conditions.

---

## 18. Relationship to Bandwidth Testing

The interworking architecture was directly related to the bandwidth measurements documented in:

`bandwidth-testing.md`

The original project observations were approximately:

| Transport | Observed combined bandwidth |
|---|---:|
| SIP/RTP with G.729 | 29–32 kbps |
| IAX2 with G.729 | 9.6–10 kbps |

The values represent historical observations from the project environment.

They are not presented as universal protocol specifications.

The original detailed captures are no longer available.

---

## 19. High-Concurrency Operation

The system was tested with more than 100 simultaneous calls.

At higher concurrency, the total traffic passing across the constrained link became more important than the bandwidth of a single call.

The IAX2 transport section was therefore intended to improve aggregate network efficiency when multiple sessions used the same path.

The exact production capacity of the original system cannot now be independently established from surviving records.

---

## 20. Configuration Files

The principal configuration files involved in the implementation were:

```text
/etc/asterisk/sip.conf
/etc/asterisk/iax.conf
/etc/asterisk/extensions.conf
```

Their roles were:

| File | Purpose |
|---|---|
| `sip.conf` | SIP peer and gateway configuration |
| `iax.conf` | IAX2 trunk configuration |
| `extensions.conf` | Dialplan and protocol-routing logic |

These files together provided the main call-control configuration for the interworking system.

---

## 21. Troubleshooting

The architecture required troubleshooting across several separate areas.

### SIP side

Issues could include:

- SIP authentication
- incorrect destination formatting
- peer reachability
- SIP response errors
- codec negotiation

### IAX2 side

Issues could include:

- trunk connectivity
- authentication
- NAT traversal
- peer reachability
- codec mismatch

### Dialplan

Issues could include:

- incorrect context
- incorrect extension matching
- wrong trunk selection
- number manipulation errors

### Media

Issues could include:

- no audio
- one-way audio
- packet loss
- jitter
- latency
- codec incompatibility

Separating these areas was important when diagnosing problems.

---

## 22. Engineering Decisions

Several deliberate engineering decisions shaped the design.

### Keep SIP interoperability

Existing Softswitch and gateway infrastructure could continue operating normally.

### Limit protocol conversion

The protocol conversion occurred only at the Asterisk systems.

### Keep the same codec

G.729 was maintained through the relevant path rather than deliberately introducing transcoding.

### Optimise only the constrained section

The architecture changed the network section where bandwidth was actually a problem.

### Use a trunk between the intermediate systems

The IAX2 trunk provided the transport relationship between the two Asterisk nodes.

---

## 23. Architecture Summary

The complete design can be summarised as:

```text
                         Existing SIP Environment
                                  |
                                  |
                                  v
                          +---------------+
                          |  Softswitch   |
                          +-------+-------+
                                  |
                               SIP/G.729
                                  |
                                  v
                          +---------------+
                          |  Asterisk 1   |
                          |   Public IP   |
                          +-------+-------+
                                  |
                            IAX2 / G.729
                                  |
                      Bandwidth-Constrained
                             Network Path
                                  |
                                  |
                                  v
                          +---------------+
                          |  Asterisk 2   |
                          | Private IP    |
                          | Behind NAT    |
                          +-------+-------+
                                  |
                               SIP/G.729
                                  |
                                  v
                          +---------------+
                          |  SIP Gateway  |
                          +---------------+
                                  |
                                  v
                         Destination Network
```

The design preserved SIP at both external boundaries while introducing an IAX2 trunk across the bandwidth-constrained section.

---

## 24. Evidence Status

This document is a retrospective technical description of engineering work carried out at Karnaphuli Online Limited in approximately 2010.

The original:

- Asterisk configuration files
- SIP configuration
- IAX2 configuration
- dialplan
- server images
- network diagrams
- screenshots
- packet captures
- bandwidth-monitoring captures

are no longer available.

The architecture described here is therefore reconstructed from my technical recollection of the implementation.

It should not be represented as contemporaneous technical documentation from 2010.

---

## 25. Evidence Boundary

The following aspects are based on the author's recollection of the original implementation:

- two Asterisk servers
- SIP input from a Softswitch
- IAX2 trunk between the Asterisk systems
- public IP on the first Asterisk server
- private IP behind NAT on the second Asterisk server
- SIP output towards a SIP gateway
- G.729 throughout the tested call path
- configuration through `sip.conf`, `iax.conf` and `extensions.conf`

Where independent documentary evidence becomes available, it should be identified separately from this retrospective technical description.

---

## Author

**Mohammad Sorower Jahan**

Digital Technology & Telecommunications Infrastructure Engineer

Technical areas:

VoIP | SIP | IAX2 | RTP | Asterisk | Linux | Network Infrastructure | Telecommunications Engineering
