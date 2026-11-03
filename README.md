![Banner](banner.png)

# net-icmpv6-scanner
Sends ICMPv6 Neighbor Solicitation, Router Solicitation, and Echo Request messages on a local network. Focused on basic network operations.

## Install
`git clone https://github.com/ShadowGuard-AI/net-icmpv6-scanner`
`pip install -r requirements.txt`

## Usage
`python3 main.py -i INTERFACE [-t TARGET] [-r] [-p] [-v]`

## Parameters
- `-h`: Show help message and exit
- `-i, --interface`: Network interface to use (e.g., eth0). Required.
- `-t, --target`: Target IPv6 address (optional, scans local network if not provided). Can be a single address or a subnet (e.g., 2001:db8::/64).
- `-r, --router-solicitation`: Send Router Solicitation messages (default: Neighbor Solicitation).
- `-p, --ping`: Send ICMPv6 Echo Request (Ping) to discovered hosts.
- `-v, --verbose`: Enable verbose output for debugging.

## Verified behavior
- `python3 main.py --help` matches the parameter list above, and `python3 -m py_compile main.py` passes. A missing `-i` is rejected by argparse with exit 2.
- With no target and no `-r`, the tool warns that Neighbor Solicitation requires a target address and stops.
- Sending is blocked in this sandbox by raw socket privileges: `-i lo -r` and `-i lo -t ::1` both print "Socket error: [Errno 1] Operation not permitted". Sending needs root or CAP_NET_RAW. That block is stated here, not recorded as a passed scan.
- Code fix in this wave: the send helper now returns success or failure, and a "Sent" line is printed only when the send actually succeeded. Before the fix, a "Sent" line followed the socket error even though nothing was sent.
- Limits, read from the code: there is no receive path, so the tool never reads a Neighbor Advertisement or Router Advertisement and never confirms a host answered. "Sent" means only that the send call succeeded. A subnet target walks every host address in the range with a fresh socket per address, with no size warning, so a large range such as a /64 is not a practical scan.

## License
See LICENSE.
