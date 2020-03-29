These are some of my points and questions while reading about the
Remote Framebuffer (RFB) protocol. And also a bit of my understandings.
A lot of times I have written the same things as the RFC, lol.

RFC 6143 - The Remote Framebuffer Protocol

Points

Introduction
1. Works at framebuffer level, so, applicable to X11, Windows, and Macintosh
2. Protocol is used in VNC
3. There's a RFB client and a RFB server
4. RFB server is the place where changes to the framebuffer originate
5. Clients are thin clients and stateless. So disconnecting and reconnecting
has no issues.

Questions:
1. What's a framebuffer?

Initial Connection
1. RFB server is a long lived process. Seems to be similar to any other server
2. RFB server maintains the state of the framebuffer
3. RFB clients typically connect, communicate with the server for some time to use and manipulate the framebuffer, then disconnect.
4. RFB server usually runs on TCP port 5900 and RFB clients connect to that port.
5900 is the standard port for RFB server according to IANA
5. If there are multiple RFB servers in a system, typically, server N listens at
port 5900+N
6. Sometimes, for the initial connection establishment, RFB server contacts
the RFB client first. For which the client listens at port 5800. Once the
connection is established, the remaining behaviors are usual based on their
roles

Display Protocol
1. This is based on a single graphics primitive: "put a rectangle of pixel
data at a given x,y position".
2. Allowing various different encodings for the pixel data gives us a 
large degree of flexibility in how to trade off various parameters such
as - network bandwidth, client drawing speed, and server processing speed.
3. A sequence of these rectangles makes a framebuffer update, also called
as **update**
4. Update - a change from one valid framebuffer state to another. Similar
to a frame of video
5. Looks like only a client's request can cause an update in the server
6. update protocol is deman-driven by the client
7. update is ONLY sent from the server to the client in response to an
explicit request from the client
8. protocol gets an adaptive quality due to point 7
9. If clients are slow, if the network is slow, the rate of update is
lower
10. With typical applications, changes to the same area of the framebuffer
tend to happen soon after one another. With a slow client or network,
transient states of the framebuffer can be ignored, resulting in less
network traffic and less drawing for the client.
11. After the initial handshake sequence, the protocol is asynchronous,
with each side sending messages as needed.
12. The server must not send unsolicited updates. An update must only
be sent in response to a request from the client.
13. When several requests from the client are outstanding, a single update
from the server may satisfy all of them.


Questions
1. what's a valid framebuffer state? and what's an invalid one?
2. Can a user from the server side cause updates? While the client is not
doing any actions or giving input but just viewing the server's screen /
windows. [Based on point 5, 12]
3. what does demand-driven mean? [Point 6]
4. what does transient states mean?
    Answer - transient means [this](https://www.merriam-webster.com/dictionary/transient)
    Looks like transient states means the states exist for some time
    and then are gone. Need to check more on it!
5. Bidirectional messaging? [Point 11] With each direction sending
messages which does not affect the other direction. Both streams/
directions being independent that is.
6. Looks like this means more like merging all the actions of sorts?
And then sending one update? Not sure. Check [Point 13]
