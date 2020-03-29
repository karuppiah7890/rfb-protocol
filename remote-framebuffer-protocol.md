These are some of my points and questions while reading about the
Remote Framebuffer (RFB) protocol. And also a bit of my understandings.
A lot of times I have written the same things as the RFC, lol.

RFC 6143 - The Remote Framebuffer Protocol

# Points

## Introduction
1. Works at framebuffer level, so, applicable to X11, Windows, and Macintosh
2. Protocol is used in VNC
3. There's a RFB client and a RFB server
4. RFB server is the place where changes to the framebuffer originate
5. Clients are thin clients and stateless. So disconnecting and reconnecting
has no issues.

### Questions:
1. What's a framebuffer?

## Initial Connection
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

## Display Protocol
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


### Questions
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

## Input Protocol
1. Input events are simply sent to the server by the client whenever
the user presses a key or pointer button, or whenever the pointing
device is moved.
2. These input events can also be synthesized from other non-standard
I/O devices. For example, a pen-based handwriting recognition engine
might generate keyboard events.

## Representation of Pixel Data
1. Initial interaction between the RFB client and server involves a
negotiation of the format and encoding of the pixel data that will be
sent.
2. This negotiation has been designed to make the job of the client
as easy as possible.
3. The server must always be able to supply pixel data in the form
the client wants.
4. Looks like it's demand based. So, server must support whatever
client wants - so, support as much formats and encoding as possible
if different kinds of clients are going to contact the server and
the client / it's features is/are not in our control
5. However, if the client is able to cope equally with several
different formats or encodings, it may choose one that is easier
for the server to produce. - So, it's not necessary for the client
to choose an easier to produce format or encoding, but it could.
So, still looks like it's based on client's demand.
6. Pixel format refers to the representation of individual colors
by pixel values.
7. The most common pixel formats are 24-bit or 16-bit "true color",
where bit-fields within the pixel value translate directly to red,
green, and blue intensities, and 8-bit "color map" (palette) where
the pixel values are indices into a 256-entry table that contains
the actual RGB intensities.
8. Encoding refers to the way that a rectangle of pixel data will
be sent to the client.
9. Every rectangle of pixel data is prefixed by a header giving the
X,Y position of the rectangle on the screen, the width and height
of the rectangle, and an encoding type which specifies the encoding
of the pixel data.
10. The data itself then follows using the specified encoding.
11. The encoding types defined at present are: Raw, CopyRect,
RRE, TRLE, Hextile, and ZRLE.
12. In practice, current servers use the ZRLE, TRLE, and CopyRect
encodings since they provide the best compression for typical
desktops. Opinion - I don't know the date and time at which this RFC
was out and if it was updated a lot and what's the last update date
and time. Not sure what current means here. And what

### Questions
1. [Point 7] How does the 24 bit and 16 bit get converted to
color? For 24-bits - looks like it's 8-bits for Red(R), 8-bits
for Green(G), 8-bits for Blue(B)? Is it? Not sure. Also, what's
the 8-bit color map? How does it relate to the point here? Is it
related to the 24-bit or 16-bit thing? indices into a 256-entry
table? I'm assuming the table has rows and colums - probably
16 rows and 16 columns, just a guess. Since the rows and columns,
can be numbered 0 to 15, the row number and column number can be
represented with 4 bits each, so totally 8 bits, is that the 8 bits
being spoken here? Big assumption I guess 😅🙈 And what are the
values in this table?
2. [Point 8] What does a rectangle of pixel data mean? a single
pixel? or many pixels in a rectangle?
3. [Point 10] What does that mean? what does data mean? And does it
mean the encoding is specified first and then data is given in that
encoding??



