# NGAP Procedure Mapping

## 1. NG Setup

**Frames:** 5 → 7

`NGSetupRequest` is sent by the gNB to establish the NG interface towards the AMF. `NGSetupResponse` confirms successful setup.

## 2. UE Registration Start

**Frame:** 41

`InitialUEMessage` carries initial UE-associated signalling from the gNB to the AMF and starts UE registration signalling at the AMF.

## 3. Authentication

**Frames:** 42 → 43

The AMF sends an authentication request using `DownlinkNASTransport`; the UE response is carried in `UplinkNASTransport`.

## 4. Security Mode

**Frames:** 44 → 45

The AMF sends a Security Mode Command through `DownlinkNASTransport`. The UE returns completion through `UplinkNASTransport`.

## 5. Initial UE Context Setup

**Frames:** 46 → 47

`InitialContextSetupRequest` is sent from AMF to gNB to establish the initial UE access context. `InitialContextSetupResponse` confirms successful context establishment.

## 6. Registration Completion and PDU Session Request

**Frame:** 49

The capture shows uplink NAS signalling associated with registration completion and a PDU Session Establishment Request. Multiple NGAP/NAS messages can be carried in one SCTP packet, so packet count and message count are not necessarily identical.

## 7. PDU Session Resource Setup

**Frames:** 51 → 53

`PDUSessionResourceSetupRequest` asks the gNB to establish the RAN-side resources associated with the PDU session. `PDUSessionResourceSetupResponse` confirms the result.

## End-to-end sequence

```text
NGSetupRequest
      ↓
NGSetupResponse
      ↓
InitialUEMessage
      ↓
DownlinkNASTransport (Authentication Request)
      ↓
UplinkNASTransport (Authentication Response)
      ↓
DownlinkNASTransport (Security Mode Command)
      ↓
UplinkNASTransport (Security Mode Complete / Registration Request)
      ↓
InitialContextSetupRequest
      ↓
InitialContextSetupResponse
      ↓
UplinkNASTransport (Registration Complete + PDU Session Request)
      ↓
DownlinkNASTransport (Configuration Update)
      ↓
PDUSessionResourceSetupRequest
      ↓
PDUSessionResourceSetupResponse
```
