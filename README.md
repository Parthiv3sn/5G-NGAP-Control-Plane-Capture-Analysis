# 5G NGAP Control-Plane Capture & Analysis

A 5G control-plane capstone project focused on capturing, decoding, and analysing the **N2 / NGAP interface** between a gNB and AMF using **Open5GS, UERANSIM, SCTP, Wireshark, and TShark**.

## Problem Statement

The N2/NGAP interface carries 5G control-plane signalling between the gNB and AMF. This project captures a UE registration and PDU session establishment sequence, decodes the NGAP messages, maps them to 5G procedures, and measures the signalling volume and observed procedure timing.

## Objectives

- Run Open5GS with UERANSIM as the 5G SA testbed.
- Capture NGAP signalling on N2.
- Decode **Initial UE Message**, **Initial Context Setup**, and **PDU Session Resource Setup**.
- Map packets to registration and PDU-session procedures.
- Measure signalling volume for the observed attach/session establishment.
- Measure the observed control-plane interval from Initial UE Message to PDU Session Resource Setup Response.

## Technology Stack

| Component | Purpose |
|---|---|
| Open5GS | 5G Core / AMF |
| UERANSIM | Simulated gNB and UE |
| SCTP | NGAP transport |
| Wireshark | Packet capture and decoding |
| TShark | Command-line analysis |
| Linux | Experimental environment |

## Architecture

```text
                  N2 / NGAP over SCTP
        +--------------------------------------+
        |                                      |
        v                                      v
+---------------+                       +---------------+
| UERANSIM gNB  | <-------------------> | Open5GS AMF   |
+-------+-------+                       +-------+-------+
        |                                       |
        v                                       v
  UERANSIM UE                              5G Core
```

## Observed Procedure Flow

```text
NG Setup
  ├── NGSetupRequest
  └── NGSetupResponse

UE Registration
  ├── InitialUEMessage
  ├── DownlinkNASTransport  (Authentication Request)
  ├── UplinkNASTransport    (Authentication Response)
  ├── DownlinkNASTransport  (Security Mode Command)
  └── UplinkNASTransport    (Security Mode Complete / Registration)

Initial UE Context Setup
  ├── InitialContextSetupRequest
  └── InitialContextSetupResponse

PDU Session Establishment
  ├── UplinkNASTransport    (Registration Complete + PDU Session Request)
  ├── PDUSessionResourceSetupRequest
  └── PDUSessionResourceSetupResponse
```

## Capture Results

The analysed capture contains the observed successful UE registration and PDU session setup:

- **13 captured packets** containing NGAP signalling.
- **14 NGAP message/procedure instances**, because one SCTP packet carries multiple NGAP messages.
- **1125 bytes** of NG Application Protocol data in the analysed output.
- **1756 bytes** of SCTP data in the analysed exchange.
- **2082 bytes** of total captured Ethernet frame data in the analysed set.
- InitialUEMessage timestamp: **93.638954 s**.
- PDUSessionResourceSetupResponse timestamp: **93.881509 s**.
- Observed interval: **0.242555 s ≈ 242.6 ms**.

## Message-to-Procedure Mapping

| Frame | NGAP message | Direction | Purpose |
|---:|---|---|---|
| 5 | NGSetupRequest | gNB → AMF | Starts N2/NGAP setup |
| 7 | NGSetupResponse | AMF → gNB | Accepts NG setup |
| 41 | InitialUEMessage | gNB → AMF | Carries initial UE NAS signalling |
| 42 | DownlinkNASTransport | AMF → gNB | Authentication Request |
| 43 | UplinkNASTransport | gNB → AMF | Authentication Response |
| 44 | DownlinkNASTransport | AMF → gNB | Security Mode Command |
| 45 | UplinkNASTransport | gNB → AMF | Security Mode Complete / Registration |
| 46 | InitialContextSetupRequest | AMF → gNB | Establishes UE access context |
| 47 | InitialContextSetupResponse | gNB → AMF | Confirms context setup |
| 49 | UplinkNASTransport | gNB → AMF | Registration Complete + PDU Session Request |
| 51 | PDUSessionResourceSetupRequest | AMF → gNB | Requests PDU-session radio resources |
| 53 | PDUSessionResourceSetupResponse | gNB → AMF | Confirms PDU-session resource setup |

## Signalling Volume

| NGAP procedure | Messages | Associated bytes |
|---|---:|---:|
| Downlink NAS Transport | 3 | 426 B |
| Initial Context Setup | 2 | 328 B |
| Initial UE Message | 1 | 138 B |
| NG Setup | 2 | 252 B |
| PDU Session Resource Setup | 2 | 368 B |
| Uplink NAS Transport | 3 | 570 B |

Detailed calculations and TShark commands are in `docs/analysis-report.md` and `analysis/tshark-commands.md`.

## Repository Structure

```text
.
├── README.md
├── CONTRIBUTORS.md
├── captures/
│   ├── ngap_capture.pcap.pcap
│   └── README.md
├── results/
│   ├── 01_amf_ngap_config.png
│   ├── 02_gnb_console_ng_setup.png
│   ├── 03_ue_console_full_attach.png
│   ├── 04_wireshark_ngap_filtered_list.png
│   ├── 05_ng_setup_request.png
│   ├── 06_ng_setup_response.png
│   ├── 07_initial_ue_message.png
│   ├── 08_downlink_nas_auth_request.png
│   ├── 09_uplink_nas_auth_response.png
│   ├── 10_downlink_nas_smc.png
│   ├── 11_uplink_nas_smc_complete.png
│   ├── 12_initial_context_setup_request.png
│   ├── 13_initial_context_setup_response.png
│   ├── 14_pdu_session_resource_setup_request.png
│   ├── 15_pdu_session_resource_setup_response.png
│   ├── 16_protocol_hierarchy_ngap.png
│   ├── 17_sctp_conversation_bytes.png
│   ├── 18_tshark_volume_breakdown.png
│   └── README.md
├── docs/
│   ├── analysis-report.md
│   └── procedure-mapping.md
└── analysis/
    └── tshark-commands.md
```

## Reproduction Steps

1. Start the Open5GS 5G Core network functions.
2. Start the UERANSIM gNB with N2/NGAP connectivity to the Open5GS AMF.
3. Start the UERANSIM UE.
4. Capture the N2/SCTP traffic in Wireshark or TShark.
5. Filter with `ngap` and inspect the procedures.
6. Use the commands in `analysis/tshark-commands.md` to reproduce the volume analysis.

## Limitations

This is a local simulated 5G SA environment. The measured timing depends on the host, virtualisation, configuration, and software versions and should not be interpreted as a commercial-network KPI. The signalling volume is specific to the captured registration/session flow.

## Future Enhancements

- Automate UE-attach procedure segmentation.
- Build an NGAP signalling dashboard.
- Compare repeated attaches statistically.
- Correlate NGAP with NAS, PFCP, and GTP-U.
- Analyse handover, paging, and UE context release.

## Contributors

- **Parthiv S N** — Project lead, testbed, capture and analysis
- **Perkmans** — Contributor / teammate

See `CONTRIBUTORS.md` for details.
