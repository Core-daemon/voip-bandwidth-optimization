# Project Evidence and Engineering Contribution

## VoIP Bandwidth Optimisation Using SIP and IAX2

**Author:** Mohammad Sorower Jahan  
**Organisation:** Karnaphuli Online Limited (KOL)  
**Role:** VoIP & Network Infrastructure R&D Manager  
**Approximate project period:** 2010  
**Project type:** Internal engineering research and operational implementation

---

## 1. Purpose of This Document

This document records the historical background, my engineering contribution and the evidence currently available for the VoIP bandwidth optimisation project.

The technical project is documented retrospectively.

The original server configurations, screenshots, packet captures and bandwidth-testing records from 2010 are no longer available.

For that reason, this document separates:

- my recollection of the engineering work
- information that can be independently confirmed
- evidence that is currently available
- evidence that is no longer available

This distinction is important when using the repository as a professional engineering record.

---

## 2. Organisation

The work was carried out at:

**Karnaphuli Online Limited (KOL)**

The project was primarily an internal telecommunications engineering and research initiative.

It was not developed as a standalone demonstration or academic exercise.

The work formed part of the ongoing technical activities of KOL's VoIP and network infrastructure operations.

---

## 3. My Role

At the time of this work, my role was:

**VoIP & Network Infrastructure R&D Manager**

My responsibilities involved practical engineering work across VoIP systems, telecommunications infrastructure, network routing and technical research.

The bandwidth optimisation work developed from an operational need to improve the use of limited network capacity while continuing to support existing SIP-based telecommunications systems.

---

## 4. Engineering Problem

The existing VoIP environment could establish calls successfully.

The engineering problem was the amount of bandwidth required when a large number of calls travelled across a constrained network connection.

As call volume increased, aggregate traffic across the network link also increased.

The project therefore investigated whether a different internal transport architecture could reduce the amount of bandwidth required without replacing the surrounding SIP infrastructure.

---

## 5. My Engineering Contribution

I personally designed the SIP-to-IAX2-to-SIP architecture used for the project.

The design was based on the following principle:

```text
Existing SIP environment
        |
        v
Asterisk 1
        |
        | IAX2
        v
Bandwidth-constrained network
        |
        | IAX2
        v
Asterisk 2
        |
        v
Existing SIP environment
```

The purpose was to retain SIP where compatibility was required while using a different transport method across the part of the network where bandwidth was limited.

---

## 6. System Design

The implemented architecture used:

- a Softswitch on the originating side
- SIP between the Softswitch and the first Asterisk server
- an IAX2 trunk between two Asterisk servers
- G.729 throughout the relevant call path
- a public IP address on the first Asterisk server
- a private IP address behind NAT on the second Asterisk server
- SIP between the second Asterisk server and the destination SIP gateway

The logical call path was:

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

---

## 7. Configuration Work

I personally configured both Asterisk servers used for the interworking architecture.

The principal Asterisk configuration files involved were:

```text
/etc/asterisk/sip.conf
/etc/asterisk/iax.conf
/etc/asterisk/extensions.conf
```

Their roles included:

| Configuration | Purpose |
|---|---|
| `sip.conf` | SIP peers, Softswitch and gateway relationships |
| `iax.conf` | IAX2 trunk between the two Asterisk systems |
| `extensions.conf` | Call routing and SIP/IAX2 interworking logic |

The original 2010 configuration files are no longer available.

Any configuration examples added to this repository later should therefore be identified as reconstructed examples rather than original historical files.

---

## 8. Bandwidth Investigation

The original network path used SIP/RTP.

I investigated whether moving the constrained section to IAX2 could reduce observed network utilisation.

G.729 was used during the comparison.

The historical observations were approximately:

| Transport arrangement | Observed combined bandwidth |
|---|---:|
| SIP/RTP with G.729 | 29–32 kbps |
| IAX2 with G.729 | 9.6–10 kbps |

Bandwidth was observed using the Linux network-monitoring utility `iftop`.

These values are historical engineering observations rather than universal protocol specifications.

---

## 9. Testing Responsibility

The bandwidth testing was carried out jointly by:

**Mohammad Sorower Jahan**

and

**Monoj Paul**

The tests involved monitoring network traffic and comparing the behaviour of the SIP/RTP and IAX2 transport arrangements.

The system was also exercised with more than 100 simultaneous calls.

The exact maximum concurrency figure from the original testing is no longer available.

---

## 10. Engineering Result

The IAX2 transport arrangement showed substantially lower observed bandwidth utilisation in the project environment.

Using representative observed values:

```text
SIP/RTP:
approximately 30 kbps

IAX2:
approximately 10 kbps
```

the difference was approximately three times.

The result can therefore be described as approximately a:

**three-times improvement in observed bandwidth efficiency**

for the project environment.

This is a project-specific engineering result and not a claim that every SIP and IAX2 implementation will produce the same ratio.

---

## 11. Operational Use

This was not only a one-off laboratory experiment.

The work formed part of ongoing KOL engineering and operational activity.

The architecture was developed in response to practical telecommunications and network-capacity requirements.

The design was intended to allow existing SIP infrastructure to remain in use while improving bandwidth utilisation across the constrained network section.

---

## 12. Independent Confirmation

The project and my role in it can be independently confirmed by:

**Monoj Paul**

who was associated with the management and technical operation of Karnaphuli Online Limited and participated with me in the bandwidth testing.

He can confirm:

- my role at KOL
- my involvement in VoIP and network infrastructure work
- the SIP-to-IAX2 bandwidth optimisation project
- my design and configuration work
- the use of Asterisk
- the bandwidth-testing activity
- the operational context of the project

---

## 13. Documentary Evidence

A current KOL recommendation/employment letter is available.

The letter includes information relating to my work at Karnaphuli Online Limited and mentions the VoIP bandwidth optimisation engineering work.

This provides independent documentary support for the historical project and my engineering contribution.

The letter itself is maintained separately from this public GitHub repository.

---

## 14. Evidence Currently Available

The evidence currently available includes:

- my retrospective technical account of the project
- current repository documentation describing the architecture
- current repository documentation describing the bandwidth tests
- current repository documentation describing SIP/IAX2 interworking
- a KOL recommendation/employment letter referring to the work
- independent confirmation available from Monoj Paul

These sources should be considered together rather than treating the retrospective GitHub documentation alone as proof of the historical work.

---

## 15. Original Evidence No Longer Available

The following original materials from approximately 2010 are no longer available:

- original Asterisk server images
- original `sip.conf`
- original `iax.conf`
- original `extensions.conf`
- original `iftop` screenshots
- packet captures
- router configurations
- historical network diagrams
- old project emails
- invoices relating specifically to the project
- detailed bandwidth-testing logs
- historical screenshots

The absence of these records is explicitly stated so that reconstructed documentation is not mistaken for contemporaneous evidence.

---

## 16. Evidence Classification

The repository evidence can be divided into three categories.

### A. Historical engineering recollection

This includes details remembered from the original implementation, such as:

- the architecture
- the protocols used
- the use of two Asterisk systems
- the G.729 codec
- the public/private network arrangement
- the bandwidth observations
- the testing scale

### B. Independent confirmation

This includes confirmation available from Monoj Paul regarding:

- the project
- my role
- the engineering implementation
- the testing activity

### C. Documentary evidence

This includes the KOL recommendation/employment letter that refers to the relevant engineering work.

These categories should remain clearly distinguished.

---

## 17. Why the Repository Was Created Later

This GitHub repository was created many years after the original engineering work.

Its purpose is to reconstruct and explain the technical design in a structured format using the information that remains available.

The repository should therefore not be interpreted as having existed in 2010.

The current Markdown files, diagrams and explanations are modern documentation of earlier engineering work.

---

## 18. Reconstructed Diagrams

The diagrams contained in this repository were created to explain the original architecture.

They are not presented as original 2010 diagrams.

They represent my reconstruction of the system based on my engineering recollection.

Where a diagram represents a reconstructed architecture, that status should remain clear.

---

## 19. Historical Bandwidth Figures

The bandwidth figures of approximately:

```text
29–32 kbps for SIP/RTP
```

and:

```text
9.6–10 kbps for IAX2
```

are retained as my recollection of measurements made during the original project using `iftop`.

The observations represented combined traffic under the test conditions used at the time.

Because the original captures no longer survive, these figures should not be described as independently reproduced measurements.

They remain historical project observations supported by the project account and independent confirmation where available.

---

## 20. Technical Contribution Summary

My contribution to the project included:

- identifying bandwidth consumption as an infrastructure constraint
- investigating an alternative VoIP transport architecture
- designing the SIP-to-IAX2-to-SIP call path
- configuring the first Asterisk server
- configuring the second Asterisk server
- configuring SIP interoperability
- configuring the IAX2 trunk
- configuring dialplan routing
- maintaining G.729 through the relevant path
- working with the NAT-based remote-side network architecture
- jointly carrying out bandwidth testing with Monoj Paul
- testing the environment with more than 100 simultaneous calls
- analysing the difference between the two transport arrangements
- applying the design within ongoing KOL operations

---

## 21. Engineering Significance

The main technical contribution was not simply the use of IAX2.

The engineering work involved recognising that the bandwidth constraint existed only on a particular part of the network and designing a solution that changed only that section.

The overall principle was:

```text
Preserve existing SIP interoperability
             +
Introduce controlled protocol conversion
             +
Optimise the constrained transport section
```

This allowed existing telecommunications systems to continue operating while reducing the observed traffic carried across the constrained path.

---

## 22. Evidence Boundaries

This repository does not claim that the present GitHub documentation itself proves the project existed in 2010.

The historical project should instead be assessed using the combination of:

- detailed technical consistency
- independent confirmation
- the KOL recommendation/employment letter
- the author's engineering account
- any additional evidence that becomes available

The repository is intended to explain the engineering work, not to manufacture historical records that no longer exist.

---

## 23. What Can Be Independently Confirmed

Monoj Paul can independently confirm the project and my involvement.

The KOL recommendation/employment letter provides additional documentary support.

Together, these provide external support for the historical engineering account even though the original server files and test screenshots are no longer available.

---

## 24. Public Evidence Handling

Employment and recommendation letters may contain:

- signatures
- telephone numbers
- email addresses
- company details
- personal information

For that reason, the original documents do not need to be published directly in the public source-code repository.

Where appropriate, evidence can be supplied separately for professional, employment or endorsement review.

---

## 25. Project Summary

The historical project can be summarised as follows:

```text
Organisation:
Karnaphuli Online Limited

Approximate period:
2010

Role:
VoIP & Network Infrastructure R&D Manager

Project type:
Internal engineering R&D and operational implementation

Primary problem:
Bandwidth limitation for high-volume VoIP traffic

Original protocol:
SIP/RTP

Optimised internal transport:
IAX2

Codec:
G.729

Interworking:
SIP -> Asterisk -> IAX2 -> Asterisk -> SIP

First Asterisk:
Public IP

Second Asterisk:
Private IP behind NAT

Measurement tool:
iftop

SIP/RTP historical observation:
approximately 29–32 kbps combined

IAX2 historical observation:
approximately 9.6–10 kbps combined

Testing scale:
more than 100 simultaneous calls

Testing:
Mohammad Sorower Jahan and Monoj Paul

Independent confirmation:
Available from Monoj Paul

Documentary evidence:
KOL recommendation/employment letter available

Original technical records:
No longer available
```

---

## 26. Conclusion

This project formed part of my telecommunications engineering work at Karnaphuli Online Limited.

I designed and configured a two-Asterisk SIP-to-IAX2-to-SIP architecture intended to reduce bandwidth use across a constrained network path while maintaining compatibility with the existing SIP infrastructure.

Bandwidth testing was carried out jointly with Monoj Paul using `iftop`, including testing with more than 100 simultaneous calls.

The historical observations showed a substantial reduction in bandwidth use when the intermediate transport was changed to IAX2.

The original technical records from approximately 2010 are no longer available, so the repository intentionally distinguishes retrospective technical documentation from independent and documentary evidence.

---

## Author

**Mohammad Sorower Jahan**

**Role at KOL:** VoIP & Network Infrastructure R&D Manager

Technical areas:

VoIP | SIP | IAX2 | RTP | Asterisk | Linux | Network Infrastructure | Telecommunications Engineering
