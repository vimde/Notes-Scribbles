# Networking

### The 4-layer internet model
* Contains Application, Transport, Network, and Link layers
* Each layer provides some service to the layer above

### The IP Service model
* When we use Internet, we use Internet Protocol to send and receive packets
* The Transport layer sends the Transport Segment to Network layer
* The Network layer puts the Segment in an IP datagram
* IP sends the datagram to the Link layer which puts it in a Link Frame
* IP is unreliable but only drops the packets if necessary
* Datagram is self-contained, it contains the Source Address, Destination Address
* Datagrams are sent via routers which contain the Forwarding Table (the next hop)
* End-to-end principle -> If we can correctly implement features at the end points, then we should, reason why network is simple
* If IP were reliable, it would not be ideal for all sorts of applications
* IP tries to prevent the packets from looping forever, this is done via TTL(hop-count) field, starts with a number and decremented
* IP fragments packets if they are too long, e.g. before they are sent to Ethernet
* New fields can be added to the header(depends on the requirements), adds additional complexity
* IP header contains Source Address, Destination Address, Protocol Id (TCP etc), Version (IPv4 or v6), TTL, Packet Id, Fragment Offset
* Checksum is calculated over the complete header

### Layering
* Modularity: breaks down the system into smaller ones
* Reuse
* Separation of concerns
* Well defined service
* Continuous improvement
