**LAST UPDATE: Sep 23, 2013**

This tool was created for audit purposes.

The idea came from these debugging tools which act as HTTP proxies (Charles Proxy, Burp Suite, Paros, Web Scarab, etc). I was looking for a similar tool which could handle any protocol and pick up packets on-the-fly, modify them, fuzz some fields and re-inject them on the wire. In other words, some kind of Wireshark with real-time packet modification ability. Of course some tools already exist such as Mallory - which is pretty good - netsed, udpproxy and others, or even Scapy for layers 1 to 4, but they are often too limited and not user-friendly.

I first considered making a Wireshark plugin but I'm not familiar with the API and I don't even know if it is possible (I don't think so). So I decided starting a new project based on several open source components:

  * [Python](http://www.python.org/) for general programming and because it's a cool language ;-)
  * [nfqueue from netfilter](http://www.netfilter.org/projects/libnetfilter_queue/doxygen/) for inline operation (and the Python binding from [alopezluksenberg](http://code.google.com/p/python-libnetfilter-queue/))
  * [tshark from Wireshark](http://www.wireshark.org/) for packet dissection
  * A custom extension of [Burp Suite Pro](http://portswigger.net/) for the GUI and its powerful tools

**Documentation: [usage](Usage.md) and [technical description](TechnicalDescription.md)**

**Lastest version: [v0.1a](http://code.google.com/p/proxyshark/downloads/detail?name=proxyshark-v0.1.tar.gz&can=2&q=) ([changelog](ChangeLog.md))**

**Coming soon: v1.0b ([todolist](ToDoList.md))**

_Nicolas G._