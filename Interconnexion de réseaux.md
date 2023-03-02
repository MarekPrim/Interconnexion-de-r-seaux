## Internetworking networks

The problem of the black box. No network should endure internal changes to connect to Internet and no users has to ask for permission to connect a device onto it.

### Usual black boxes :
- Physical -> Hub, repeat and amplify. Very simple and cheap but got no inteligence as he propagate any signal he receives.
- Bridge, same behavior as a hub but with more intelligence (level 2) with an MAC/Port table

## Layer 2 interconnection Ethernet-Wifi

#### Encapsulation vs Translation

No solution is perfect

###### Encapsulation : Package the data into another packet, the original packet is now the payload

Advantages :
- Don't need to understand the protocol fully
- Near no computing required

Weaknesses :
- The packet will be longer


###### Translation : Extract data in the header and reform a packet

Advantages :
- Less overhead

Weaknesses :
- More computing power required

Ethernet is encapsulated in Wifi

### Difference between Ethernet and I3E 802.3 ?


There's no LLC in Ethernet, in contrary to 802.3.
For the same byte space, Ethernet uses protocol type, 802.3 uses length.
For 802.3, protocol type is enclosed in the LLC frame.
SNAP is used to remember the ID of the packet (this used to be an Ethernet packet) -> SNAP OUI becomes a specific value for his first byte : 00 00 00 + Type (no org uses this ID for reasons, so why not put it in the standard) [LCC is set to AA AA to indicate SNAP usage]
This is how 802.3 can be switched to Ethernet and the other way around.

Apple and Novel Inc. released protocols that did not fit with the existing standard -> Creation a new standard 802.11h to add Ethertype for AARP and IPX so that all bridges will understand those protocols.

**Else if Type field contains 0x80F3 or 0x8137 -> AppleTalk or IPX (802.11h)**


