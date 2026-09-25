# VoIP Bandwidth Optimisation

## SIP-to-IAX2 Transport Architecture for Bandwidth-Constrained Networks

**Author:** Mohammad Sorower Jahan  
**Engineering Area:** VoIP, Telecommunications Infrastructure and Network Optimisation

---

## Project Overview

This repository documents an engineering project I carried out while investigating ways to reduce bandwidth consumption across constrained VoIP network links.

The main issue was not the ability to establish a call. SIP calls were already working. The problem was the amount of bandwidth required when a significant number of calls had to cross a limited-capacity network connection.

During testing, I compared the bandwidth behaviour of the existing SIP transport path with an alternative architecture using IAX2 between the two intermediate VoIP systems.

The resulting design kept SIP at the external edges of the network while using IAX2 across the bandwidth-constrained section.

The basic idea was:

```text
SIP Network
     |
     v
VoIP Gateway / Asterisk
     |
     | SIP converted to IAX2
     v
IAX2 Transport
     |
     | Bandwidth-constrained network
     v
IAX2 Transport
     |
     | Converted back to SIP
     v
Local SIP Gateway
     |
     v
Destination Network
