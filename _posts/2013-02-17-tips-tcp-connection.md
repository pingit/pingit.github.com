---
layout: post
title: "tips: tcp connection"
description: ""
category: 
tags: [TCP]
---
{% include JB/setup %}

[tcp/ip illustrated](ftp://ftp.itb.ac.id/pub/ISO-IMAGES/linux/filenya-putu-shinoda/bukulinux/the-protocols-tcp-ip-illustrated-volume-1.9780201633467.24290.pdf) Ch18.11

## fork
most TCP servers are concurrent. When a new connection request arrives at a
server, the server accepts the connection and invokes a new process to handle
the new client. Depending on the operating system, various techniques are used
to invoke the new server. Under Unix the common technique is to create a new
process using the fork function.  Lightweight processes (threads) can also be
used, if supported.

A [[concurrent]] server invokes a new process to handle each client, so the
listening server should always be ready to handle the next incoming connection
request. That's the underlying reason for using concurrent servers. But there
is still a chance that multiple connection requests arrive while the listening
server is creating a new process, or while the operating system is busy running
other higher priority processes.

## backlog (queue)
backlog value specifies only the maximum number of queued connections for one
listening end point, all of which have already been accepted by TCP and are
waiting to be accepted by the application. This backlog has no effect
whatsoever on the maximum number of established connections allowed by the
system, or on the number of clients that a concurrent server can handle
concurrently

## what 'should' happen when backlog is full?
TCP ignores the incoming SYN when the queue is full, and doesn't respond with
an RST, because this is a soft error, not a hard error. Normally the queue is
full because the application or the operating system is busy, preventing the
application from servicing incoming connections. This condition could change in
a short while. But if the server's TCP responded with a reset, the client's
active open would abort (which is what we saw happen if the server wasn't
started). By ignoring the SYN, the server forces the client TCP to retransmit
the SYN later, hoping that the queue will then have room for the new
connection.

## a *very* subtle issue: connection arrival vs. acceptance
TCP accepts an incoming connection request (i.e., a SYN) if there is room on
the listener's queue, without giving the application a chance to see who it's
from (the source IP address and source port number) This is not required by
TCP, it's just the common implementation technique (i.e., the way the Berkeley
sources have always done it). If an API such as TLI gives the
application a way to learn when a connection request arrives, and then allows
the application to choose whether to accept the connection or not, be aware
that with TCP, when the application is supposedly told that the connection has
just arrived, TCP's three-way handshake is over! Other transport layers may be
implemented to provide this separation to the application between arrival and
acceptance (i.e., the OSI transport layer) but not TCP.

This behavior also means that a TCP server has no way to cause a client's
active open to fail.  When a new client connection is passed to the server
application, TCP's three-way handshake is over, and the client's active open
has completed successfully. If the server then looks at the client's IP address
and port number, and decides it doesn't want to service this client, all the
server can do is either close the connection (causing a FIN to be sent) or
reset the connection (causing an RST to be sent). In either case the client
thought everything was OK when its active open completed, and may have already
sent a request to the server. 


## timeline

         .--------------------------------------------------------.
         |                          APP                           |
         |                                                        |
         |                                                        |
         |                                                        |
         '--------------------------------------------------------'
          \                                          \
           \                                          \
            \                                          \
       1:listen                                   6:APP take 
       port 23                                    connection 
               \                                          \
                \                                          \
                 \                                          \
        .---------v---------------------------.--------------v.-----.
        |                 3:check backlog   ^ |  LEP backlog  |     |
        |                 of the LEP       /  '---------------'     |
        |                     |           /                         |
        |                  if yes       5:TCP accept connection(s)  |
        |                     |         & push into backlog         |
        |                     |        /                            |
        |                     v                                     |
        |             ^                                             |
        |             |   4:three-way                               |
        |             |   handshake                                 |
        |             |                                             |
        |             |       |   ^                                 |
        '-------------|-------|---|---------------------------------'
                      |       |   |
                      |       |   |
                      |     ACK   |SYN
                      |       |   |
                      |       |   |
                      |       v   |

                     2:SYN 
                     arrive


