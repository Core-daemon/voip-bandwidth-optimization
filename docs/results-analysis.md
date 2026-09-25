# Results and Engineering Analysis

## VoIP Bandwidth Optimisation Using SIP and IAX2

**Author:** Mohammad Sorower Jahan  
**Organisation:** Karnaphuli Online Limited (KOL)  
**Approximate project period:** 2010  
**Codec:** G.729  
**Measurement tool:** iftop

---

## 1. Purpose

This document summarises the engineering results of the SIP-to-IAX2 bandwidth optimisation project.

The work investigated whether changing the transport architecture across a bandwidth-constrained network segment could reduce the amount of traffic required for VoIP communication.

The external telecommunications environment remained SIP-based.

IAX2 was introduced only between the two intermediate Asterisk systems.

The principal comparison was therefore between:

```text
SIP/RTP + G.729
```

and:

```text
IAX2 + G.729
```

across the constrained transport section.

---

## 2. Original Problem

The original system could establish and complete VoIP calls successfully.

The main limitation was network capacity.

As the number of simultaneous calls increased, the total bandwidth required across the constrained connection also increased.

This created an engineering problem:

```text
More simultaneous calls
        |
        v
Higher aggregate bandwidth utilisation
        |
        v
Greater pressure on the constrained network link
```

The objective was therefore to improve transport efficiency without replacing the surrounding SIP infrastructure.

---

## 3. Architecture Used

The implemented call path was:

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
Bandwidth-Constrained Network
    |
    v
Asterisk 2
Private IP behind NAT
    |
    | SIP / G.729
    v
SIP Gateway
```

The two Asterisk servers formed the interworking layer.

Asterisk 1 converted the incoming SIP call into the IAX2 transport path.

Asterisk 2 received the IAX2 call and passed it back into the SIP environment.

---

## 4. Measurement Results

During the original tests, bandwidth was monitored using `iftop`.

The representative historical observations were:

| Transport arrangement | Observed combined bandwidth |
|---|---:|
| SIP/RTP with G.729 | approximately 29–32 kbps |
| IAX2 with G.729 | approximately 9.6–10 kbps |

These figures represent the values observed in the project environment.

They are not presented as theoretical protocol specifications.

---

## 5. Relative Reduction

Using representative values:

```text
SIP/RTP:
approximately 30 kbps

IAX2:
approximately 10 kbps
```

the observed ratio was approximately:

```text
30 / 10 = 3
```

The IAX2 transport arrangement therefore showed approximately a:

**three-times improvement in observed bandwidth efficiency**

under the conditions used during the project.

Another way of expressing the same observation is that the IAX2 path used roughly one-third of the bandwidth observed on the original SIP/RTP path.

---

## 6. Percentage Illustration

Using the representative values of 30 kbps and 10 kbps:

```text
Difference = 30 - 10
           = 20 kbps
```

Relative reduction:

```text
20 / 30 x 100
= approximately 66.7%
```

This provides a simple illustration of the scale of the observed difference.

It should not be interpreted as a universal claim that IAX2 reduces SIP bandwidth by 66.7%.

The calculation applies only to the representative values observed in this project.

---

## 7. Effect at Higher Call Volumes

The project was tested with more than 100 simultaneous calls.

At this scale, a bandwidth difference that appears relatively small for an individual call becomes much more important.

Using representative project values for 100 calls:

### SIP/RTP example

```text
100 x 30 kbps
= approximately 3,000 kbps
= approximately 3 Mbps
```

### IAX2 example

```text
100 x 10 kbps
= approximately 1,000 kbps
= approximately 1 Mbps
```

The difference in this simplified example is approximately:

```text
2 Mbps
```

This illustrates the reason the optimisation became operationally useful as concurrency increased.

---

## 8. More Than 100 Simultaneous Calls

The system was exercised with more than 100 simultaneous calls during the original work.

The exact maximum number from the 2010 testing is no longer available.

For this reason, the repository records the scale as:

**more than 100 simultaneous calls**

rather than claiming a more precise capacity figure that cannot now be independently supported.

---

## 9. Importance of Keeping the Codec Constant

G.729 was used on both the SIP/RTP and IAX2 paths.

This was important because the purpose of the comparison was to evaluate the transport architecture rather than compare different codecs.

Conceptually:

```text
Test 1:
G.729 + SIP/RTP

Test 2:
G.729 + IAX2
```

Keeping the codec consistent reduced one major variable in the comparison.

The main engineering change was therefore the transport method used between the intermediate systems.

---

## 10. Codec Bitrate Versus Observed Network Traffic

The codec bitrate should not be confused with the total traffic observed on a network interface.

A VoIP system includes additional network behaviour beyond the encoded speech payload.

The observed traffic can be affected by:

- packet headers
- protocol framing
- packetisation interval
- speech activity
- silence periods
- voice activity detection
- trunking behaviour
- interface measurement
- averaging interval
- call signalling
- network-layer overhead

For this reason, the project results are documented as observed network measurements rather than theoretical codec calculations.

---

## 11. Interpreting the 9.6–10 kbps Observation

The historical IAX2 observation of approximately 9.6–10 kbps represented what was seen in `iftop` for both directions of the tested call under the conditions used at the time.

That figure is lower than would normally be expected for continuously active bidirectional G.729 codec payload alone.

Possible factors include:

- conversational speech rather than continuous audio in both directions
- silence periods
- voice activity detection or silence suppression
- IAX2 trunking behaviour
- the averaging interval displayed by `iftop`
- measurement timing
- interface-level observation behaviour

The original packet captures and detailed 2010 test records are no longer available.

The value is therefore preserved as a historical engineering observation rather than presented as a reproducible protocol benchmark.

---

## 12. Why the Result Was Useful

The optimisation was useful because the network constraint existed in only one part of the overall telecommunications path.

Instead of replacing SIP throughout the environment, the design changed only the transport method between the two intermediate systems.

This provided a practical engineering balance:

```text
Existing SIP compatibility
          +
Internal IAX2 transport
          +
Lower observed bandwidth
```

The surrounding Softswitch and SIP gateway could continue using their existing protocols.

---

## 13. Operational Benefit

The main operational benefit was improved use of the available network capacity.

Where bandwidth was limited, reducing the traffic required per active session allowed more calls to share the same network resource before bandwidth became the primary constraint.

This did not mean that bandwidth was the only system limitation.

Other factors could still affect overall capacity, including:

- CPU resources
- memory
- codec handling
- disk and logging activity
- network latency
- packet loss
- jitter
- server configuration
- concurrent signalling load

The project addressed the network-bandwidth component of this wider capacity problem.

---

## 14. Protocol Boundary as an Engineering Strategy

An important part of the design was the use of controlled protocol boundaries.

The architecture can be simplified as:

```text
Existing protocol
      |
      v
Conversion point
      |
      v
Optimised transport
      |
      v
Conversion point
      |
      v
Existing protocol
```

This allowed the optimisation to remain isolated from the systems that did not need to change.

The same engineering principle can be applied in other infrastructure projects where a constraint exists only in part of a larger system.

---

## 15. NAT and Remote-Side Design

Asterisk 1 used a public IP address.

Asterisk 2 operated on a private IP address behind NAT.

The IAX2 trunk connected these two systems across the network.

This topology was relevant because the remote system did not have the same direct public addressing arrangement as the first server.

The original firewall and NAT configuration from 2010 has not been retained, so this repository does not attempt to reconstruct exact historical rules.

---

## 16. No Intentional Transcoding

G.729 was used throughout the relevant call path.

The design did not intentionally depend on converting the voice stream to another codec as part of the optimisation.

This helped avoid adding an unnecessary transcoding stage.

Avoiding intentional transcoding also reduced the risk of:

- additional CPU load
- increased latency
- avoidable codec-quality changes

The optimisation focused on the transport architecture.

---

## 17. SIP Interoperability Was Preserved

The Softswitch communicated with the first Asterisk system using SIP.

The destination SIP gateway also communicated through SIP.

This meant the internal transport optimisation did not require the surrounding platforms to be converted to IAX2.

The external view of the architecture therefore remained:

```text
SIP system
    |
    |
Intermediate network
    |
    |
SIP system
```

while internally the constrained transport section used IAX2.

---

## 18. Engineering Outcome

The project demonstrated a practical method for reducing observed VoIP bandwidth usage across a constrained network path.

The result was achieved through architecture rather than simply replacing the codec.

The main elements were:

- retain SIP at the network edges
- introduce Asterisk as the protocol boundary
- use IAX2 between the intermediate servers
- retain G.729
- monitor actual interface traffic
- compare the two transport arrangements
- test the design with more than 100 simultaneous calls

---

## 19. Result Summary

The historical project observations can be summarised as:

```text
Organisation:
Karnaphuli Online Limited

Approximate year:
2010

Input:
SIP from Softswitch

Intermediate system:
Asterisk 1

Constrained transport:
IAX2 trunk

Remote intermediate system:
Asterisk 2 behind NAT

Output:
SIP to SIP gateway

Codec:
G.729

Measurement:
iftop

SIP/RTP observation:
approximately 29–32 kbps combined

IAX2 observation:
approximately 9.6–10 kbps combined

Test scale:
more than 100 simultaneous calls

Observed bandwidth efficiency:
approximately three times
```

---

## 20. Evidence Limitations

The original technical evidence from 2010 is no longer available.

This includes:

- original server images
- `sip.conf`
- `iax.conf`
- `extensions.conf`
- `iftop` screenshots
- packet captures
- router configuration
- historical network diagrams
- detailed test logs

The current repository is therefore a retrospective technical reconstruction based on the author's recollection of the original work.

This distinction is intentionally stated throughout the repository.

---

## 21. What the Results Support

The historical results support the description of an engineering project that investigated and implemented a SIP-to-IAX2 transport architecture for bandwidth-constrained VoIP communication.

The project can reasonably be described as having:

- compared SIP/RTP and IAX2 transport behaviour
- retained G.729 during the comparison
- used `iftop` for bandwidth observation
- implemented SIP-to-IAX2-to-SIP interworking
- used two Asterisk servers
- used an IAX2 trunk between them
- tested at more than 100 simultaneous calls
- observed a substantial reduction in interface traffic

---

## 22. What the Results Do Not Establish

The historical observations do not independently establish that:

- all SIP systems will reproduce the same bandwidth figure
- all IAX2 systems will reproduce the same bandwidth figure
- IAX2 is universally three times more efficient
- the values represent theoretical codec bandwidth
- every call had identical traffic behaviour
- the original measurements can now be reproduced exactly

The figures should therefore remain tied to the specific project environment.

---

## 23. Engineering Significance

The most important result was not the numerical ratio by itself.

The engineering significance was demonstrating that a constrained transport segment could be redesigned without replacing the complete telecommunications environment.

The architecture isolated the optimisation to the part of the network that needed it.

That allowed:

- continued SIP interoperability
- controlled protocol conversion
- reduced observed transport traffic
- operation with high call concurrency
- minimal change to surrounding systems

---

## 24. Conclusion

The project addressed a practical telecommunications infrastructure problem: carrying a large number of VoIP sessions across a network where available bandwidth was limited.

The implemented solution retained SIP at both external boundaries and introduced IAX2 between two Asterisk systems across the constrained section.

Under the original test conditions, this design showed substantially lower observed bandwidth utilisation than the existing SIP/RTP transport arrangement.

The representative historical measurements were approximately 29–32 kbps for the SIP/RTP path and 9.6–10 kbps for the IAX2 path, with both observations recorded as combined traffic using `iftop`.

The measurements should be understood as project-specific engineering observations rather than universal protocol benchmarks.

---

## Author

**Mohammad Sorower Jahan**

Digital Technology & Telecommunications Infrastructure Engineer

Technical areas:

VoIP | SIP | IAX2 | RTP | Asterisk | Linux | Network Infrastructure | Telecommunications Engineering
