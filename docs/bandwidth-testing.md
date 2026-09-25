# Bandwidth Testing and Measurement

## SIP-to-IAX2 VoIP Bandwidth Optimisation

**Author:** Mohammad Sorower Jahan  
**Organisation:** Karnaphuli Online Limited (KOL)  
**Approximate project period:** 2010  
**Codec used:** G.729  
**Measurement tool:** iftop

---

## 1. Purpose of the Testing

The purpose of this testing was to investigate whether the amount of bandwidth used by VoIP traffic could be reduced across a network link where available capacity was limited.

The existing environment used SIP-based VoIP communication.

The problem was not basic call establishment. Calls could already be completed successfully.

The problem became more important when a large number of simultaneous calls had to cross the same network path.

I therefore compared the bandwidth behaviour of the existing SIP/RTP transport arrangement with an alternative design using IAX2 between intermediate VoIP systems.

---

## 2. Test Environment

The tests were carried out while I was working at Karnaphuli Online Limited in approximately 2010.

G.729 was used for the voice codec on both sides of the comparison.

The two arrangements being compared were:

### Existing transport

```text
SIP
 |
 v
RTP / G.729
 |
 v
Network
 |
 v
SIP
```

### Optimised transport

```text
SIP
 |
 v
VoIP Interworking System
 |
 | IAX2 / G.729
 v
Bandwidth-Constrained Network
 |
 | IAX2 / G.729
 v
Remote VoIP Interworking System
 |
 v
SIP
```

The external telecommunications systems continued using SIP.

The main change was the transport method used across the constrained section of the network.

---

## 3. Measurement Tool

Bandwidth was observed using the Linux command-line monitoring utility:

`iftop`

I used `iftop` to monitor traffic passing through the relevant network interface while VoIP calls were active.

The objective was to compare practical network utilisation under the two transport arrangements rather than calculate bandwidth only from theoretical protocol header sizes.

---

## 4. Codec

The codec used during the tests was:

**G.729**

G.729 was already suitable for bandwidth-sensitive voice environments because of its relatively low encoded voice bitrate.

However, the total bandwidth consumed by a VoIP call is not determined by codec bitrate alone.

Network traffic also contains protocol overhead associated with:

- IP
- UDP
- RTP
- SIP signalling
- IAX2 framing
- Ethernet
- packet timing
- other network-layer overhead

The project therefore focused on actual observed network traffic.

---

## 5. SIP/RTP Observation

During the SIP/RTP tests, the observed bandwidth for a call was approximately:

**29–32 kbps**

The figure represented the traffic I observed through `iftop` for both directions of the call under the test conditions used at the time.

This value is retained here as a historical project observation.

It should not be treated as a universal bandwidth specification for G.729 or SIP/RTP.

---

## 6. IAX2 Observation

When the transport architecture was changed to IAX2 between the intermediate VoIP systems, the observed bandwidth was approximately:

**9.6–10 kbps**

This was also observed through `iftop` for both directions of the tested call under the conditions used during the project.

The same G.729 codec was used.

The result showed a substantial reduction in the amount of traffic observed across the constrained network section.

---

## 7. Important Interpretation of the Measurements

The measurements above are historical engineering observations rather than protocol specifications.

A G.729 encoded voice stream has its own nominal payload bitrate before network overhead is added.

For that reason, the observed combined figures should not be interpreted as the theoretical continuously active full-duplex bitrate of G.729 itself.

The observed results may have been affected by factors including:

- conversational speech activity
- silence periods
- silence suppression or voice activity detection
- IAX2 framing behaviour
- IAX2 trunking
- `iftop` averaging intervals
- measurement point
- packetisation interval
- traffic direction
- call activity at the time of observation
- network-interface behaviour

The original packet captures and detailed measurement records are no longer available.

For this reason, the figures are documented as the values observed during the original engineering work rather than presented as independently reproducible protocol benchmarks.

---

## 8. Relative Bandwidth Difference

Using representative values from the observations:

```text
SIP/RTP:
approximately 30 kbps

IAX2:
approximately 10 kbps
```

the IAX2 transport path showed roughly one-third of the bandwidth observed on the SIP/RTP path.

The practical result was therefore approximately a:

**three-times improvement in observed bandwidth efficiency**

for the tested environment.

This comparison refers only to the project configuration and test conditions.

It is not a claim that IAX2 always uses one-third of the bandwidth of SIP.

---

## 9. Higher Call Volumes

The architecture was also tested with large numbers of simultaneous VoIP sessions.

During testing, the environment was operated with:

**more than 100 simultaneous calls**

The purpose of the higher-load testing was to determine whether the bandwidth saving remained useful when many calls shared the constrained network connection.

The exact maximum concurrency figure from the original 2010 testing has not been preserved.

For this reason, the repository records the tested scale only as more than 100 simultaneous calls.

---

## 10. Why Multiple Calls Mattered

A small difference in bandwidth for one call can become significant when many calls share the same network connection.

Using simplified representative project values:

```text
100 calls x approximately 30 kbps
= approximately 3,000 kbps
```

compared with:

```text
100 calls x approximately 10 kbps
= approximately 1,000 kbps
```

This example illustrates the engineering reason for investigating the alternative transport architecture.

It is a simple multiplication of representative observed values and should not be interpreted as a precisely measured production capacity figure.

---

## 11. Test Process

The testing process involved several stages.

### Stage 1 - Existing SIP path

Calls were established through the existing SIP-based environment.

Bandwidth utilisation was observed using `iftop`.

The approximate traffic behaviour was recorded during active calls.

### Stage 2 - IAX2 transport

The intermediate transport was changed so that calls crossed the constrained network section using IAX2.

The external call legs continued using SIP.

Bandwidth was again observed using `iftop`.

### Stage 3 - Comparison

The two arrangements were compared using the observed network utilisation.

The main question was whether changing the internal transport could reduce bandwidth without requiring the surrounding SIP infrastructure to be replaced.

### Stage 4 - Higher concurrency

The architecture was then exercised with more than 100 simultaneous calls.

This was used to assess the practical behaviour of the design when the constrained network carried a larger number of sessions.

---

## 12. What Was Kept Constant

The comparison used G.729 on both sides.

Keeping the codec consistent was important because changing the codec at the same time would have made it more difficult to understand how much of the observed difference related to transport architecture.

The principal comparison was therefore between:

```text
SIP/RTP + G.729
```

and:

```text
IAX2 + G.729
```

across the constrained network section.

---

## 13. What Changed

The main change was the transport architecture.

The surrounding SIP environment remained in place.

Instead of carrying the calls across the constrained section using the original SIP/RTP arrangement, intermediate VoIP systems handled the protocol transition and transported the calls through IAX2.

This allowed the optimisation to be introduced without requiring all connected telecommunications systems to support IAX2.

---

## 14. Engineering Result

The testing demonstrated that the alternative transport architecture could substantially reduce the traffic observed on the constrained link under the project conditions.

The representative observations were:

| Test arrangement | Observed combined bandwidth |
|---|---:|
| SIP/RTP with G.729 | approximately 29–32 kbps |
| IAX2 with G.729 | approximately 9.6–10 kbps |

The observed reduction was approximately three times.

The important engineering result was not simply a comparison between two protocol names.

The result came from changing the architecture of the constrained part of the network while preserving SIP interoperability at the external boundaries.

---

## 15. Measurement Limitations

Several limitations apply to the historical measurements.

### Original captures are no longer available

The original `iftop` screenshots, packet captures and detailed logs from 2010 have not been retained.

### Exact averaging interval is not available

The precise `iftop` averaging column or time interval used during the original observation is no longer known.

### Speech activity may affect observations

Voice traffic can vary depending on whether one or both participants are actively speaking.

### Silence suppression may affect traffic

Where voice activity detection or silence suppression is present, observed traffic may be lower than continuously active codec calculations.

### Packetisation details are not preserved

The original packetisation interval used during the tests is not available in the surviving records.

### The figures are not laboratory benchmarks

The measurements were taken as part of practical telecommunications engineering work.

They were not produced as a formal standards-compliance or academic laboratory benchmark.

---

## 16. Evidence Status

The bandwidth figures in this document are based on my recollection of the original engineering measurements carried out at Karnaphuli Online Limited in approximately 2010.

The original:

- screenshots
- `iftop` captures
- packet captures
- server configuration
- router configuration
- measurement logs

are no longer available.

For this reason, the figures are presented as historical engineering observations.

They should not be represented as independently verified measurements unless additional historical evidence becomes available.

---

## 17. What Can Be Claimed

Based on the original engineering work, the project can be described as an investigation and implementation of a SIP-to-IAX2 transport architecture intended to reduce bandwidth consumption across constrained VoIP network links.

The historical observations indicated approximately:

- 29–32 kbps for the tested SIP/RTP path
- 9.6–10 kbps for the tested IAX2 path
- approximately three-times improvement in observed bandwidth efficiency
- testing at a scale exceeding 100 simultaneous calls

These values are specific to the original implementation.

---

## 18. What Should Not Be Claimed

The project should not be described as proving that:

- every G.729 SIP call consumes 29–32 kbps
- every IAX2 call consumes 9.6–10 kbps
- IAX2 always reduces bandwidth by three times
- all networks will produce the same result
- the figures are theoretical codec bitrates
- the measurements have been independently reproduced from original 2010 records

The documented result is a project-specific engineering observation.

---

## 19. Testing Summary

The original work can be summarised as:

```text
Organisation:
Karnaphuli Online Limited

Approximate year:
2010

Codec:
G.729

Measurement tool:
iftop

SIP/RTP observation:
approximately 29–32 kbps combined

IAX2 observation:
approximately 9.6–10 kbps combined

Concurrency tested:
more than 100 simultaneous calls

Observed improvement:
approximately three times

Original measurement evidence currently available:
No
```

---

## Author

**Mohammad Sorower Jahan**

Digital Technology & Telecommunications Infrastructure Engineer

Technical areas:

VoIP | SIP | IAX2 | RTP | Asterisk | Linux | Network Infrastructure | Telecommunications Engineering
