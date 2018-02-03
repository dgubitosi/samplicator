UDP Samplicator
===============

This small program receives UDP datagrams on a given port, and resends
those datagrams to a specified set of receivers.  In addition, a
sampling divisor N may be specified individually for each receiver,
which will then only receive one in N of the received packets.

DOWNLOADING
-----------

This package is distributed under
	https://github.com/sleinen/samplicator/

INSTALLATION
------------

See [INSTALL.md](INSTALL.md).

AUTHORS
-------

See the `AUTHORS` file.

USAGE
-----

The usage convention for the program is

	$ samplicate [<option>...] [<destination>...]

Where each `<option>` can be one of

	-d <level>	to set the debugging level
	-s <address>	to set interface address on which to listen
			for incoming packets (default any)
	-p <port>	to set the UDP port on which to listen for
			incoming packets (default 2000)
	-b <buflen>	size of receive buffer (default 65536)
	-c <configfile>	specify a config file to read
	-x <delay>	to specify a transmission delay after each packet,
			in units of microseconds
	-S		maintain (spoof) source addresses
	-n		don't compute UDP checksum (only relevant with -S)
	-f		fork program into background
	-m <pidfile>	write the process ID to a file
	-4		IPv4 only
	-6		IPv6 only
	-h		to print a usage message and exit
	-u <pdulen>	size of max pdu on listened socket (default 65536)
	-X		use command line receivers for unmatched sources\n\

and each `<destination>` should be specified as
`<addr>[/<port>[/<interval>[,ttl]]]`, where

	<addr>		IP address of the receiver
	<port>		port UDP number of the receiver (default 2000)
	<freq>		number of received datagrams between successive
			copied datagrams for this receiver.
	<ttl>		The TTL (IPv4) or hop-limit (IPv6) for
			outgoing datagrams.

Config file format:

	source: [receiver ...]

	a.b.c.d[/e.f.g.h]: receiver ...
	a.b.c.d[/e.f.g.h]:
	*: receiver ...

where:

	a.b.c.d                  is the senders IP address
	e.f.g.h                  is a mask to apply to the sender (default 255.255.255.255)
	*                        denotes receivers for unmatched sources
	receiver                 see above

The source address of incoming packets is matched against three categories in
order: 1) blacklist, 2) standard, 3) unmatched.
Source addresses will only be processed by the first category where a match
is found.

Receivers from **all** matching sources defined in the configuration will get
copies as sources may overlap with masks.  Sources defined without a receiver
are the exception.  They are explicitly blacklisted and dropped before any
further matches are processed.

The wildcard source is used to denote the unmatched category.  A source address
that does not match any known sources from the configuration are processed here.
Unmatched sources are dropped unless the wildcard source is explicitly defined.

Any receivers defined on the command line will get all packets as they match
as 0.0.0.0/0. The -X option changes this behavior and places all command line
receivers in the unmatched category.

