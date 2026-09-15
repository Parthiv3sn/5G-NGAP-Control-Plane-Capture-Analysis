# NGAP Capture Analysis Report

## 1. Introduction

The N2 interface is the 5G control-plane interface between the gNB and AMF. NGAP provides the signalling procedures required for UE access, UE context management and coordination of PDU session resources. This project captures and analyzes this signalling using a local Open5GS and UERANSIM deployment.

## 2. Experimental Environment

- 5G Core: Open5GS
- RAN/UE simulator: UERANSIM
- Packet analyzer: Wireshark
- Command-line analyzer: TShark
- Transport: SCTP
- Interface under study: N2 / NGAP

## 3. Capture Scope

The supplied capture contains NGAP signalling covering NG Setup, UE registration, authentication/security-related NAS transport, Initial Context Setup and PDU Session Resource Setup.

## 4. Main Findings

The capture contains 13 NGAP packets and 14 NGAP messages/procedure instances. The difference occurs because at least one SCTP packet contains multiple NGAP messages.

The measured byte counts are:

- NGAP: 1125 B
- SCTP: 1756 B
- Total captured Ethernet frame data: 2082 B

The observed Initial UE Message occurs at 93.638954 s and the PDU Session Resource Setup Response occurs at 93.881509 s, giving an observed interval of 242.555 ms.

## 5. Procedure Analysis

### NG Setup

Frames 5 and 7 establish the NGAP relationship between the simulated gNB and AMF.

### UE Registration

Frame 41 (`InitialUEMessage`) starts UE-associated signalling. Subsequent NAS messages are transported using NGAP `DownlinkNASTransport` and `UplinkNASTransport` procedures.

### Initial Context Setup

Frames 46 and 47 show the AMF requesting and the gNB confirming initial UE context setup.

### PDU Session Resource Setup

Frames 51 and 53 show the request/response exchange used to establish the RAN-side resources for the PDU session.

## 6. Signalling Volume

The supplied TShark output associates the following captured frame-byte volumes with NGAP procedures:

| Procedure | Messages | Bytes |
|---|---:|---:|
| Downlink NAS Transport | 3 | 426 B |
| Initial Context Setup | 2 | 328 B |
| Initial UE Message | 1 | 138 B |
| NG Setup | 2 | 252 B |
| PDU Session Resource Setup | 2 | 368 B |
| Uplink NAS Transport | 3 | 570 B |

These are packet/capture byte associations and should not be interpreted as pure ASN.1 NGAP payload lengths.

## 7. Conclusion

The experiment successfully demonstrates packet-level observation of the N2 control plane in a simulated 5G network. The capture can be correlated from NG Setup through UE registration and initial context establishment to PDU Session Resource Setup. The combination of Wireshark decoding and TShark statistics provides both qualitative procedure mapping and quantitative signalling-volume measurement.

## 8. Recommended Extensions

A stronger experimental study would repeat the same procedure over multiple independent attaches, calculate latency distributions, separate registration-only and PDU-session signalling, and automate the procedure extraction from PCAP files.
