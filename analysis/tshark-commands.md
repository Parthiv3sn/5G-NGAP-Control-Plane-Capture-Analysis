# TShark Analysis Notes

Useful commands for reproducing the packet-level analysis are below. Exact field names and statistics formatting can vary by Wireshark/TShark release.

```bash
# Show NGAP packets
tshark -r captures/ngap_capture.pcap.pcap -Y ngap

# Show SCTP packets
tshark -r captures/ngap_capture.pcap.pcap -Y sctp

# Protocol hierarchy statistics
tshark -r captures/ngap_capture.pcap.pcap -z io,phs

# SCTP conversation statistics
tshark -r captures/ngap_capture.pcap.pcap -z conv,sctp
```

For reproducibility, record the Wireshark/TShark version used for each analysis run.
