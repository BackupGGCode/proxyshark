**LAST UPDATE: Sep 30, 2013**

#### `==` v1.0b (coming soon) `==` ####

  * Added internal caching mechanism (better performance)
  * Added _file_ and _syslog_ logging handlers
  * Added logging coloration
  * Improved verbosity levels
  * Added capture filters based on BPF filters (you don't need to write your own _iptables_ rules)
  * Added packet filters inspired from _Wireshark_ display filters
  * Added field filters based on regular expressions
  * Enhanced search mechanism in dissected packets (based on _xpath_)
  * Added local web server for the web-driven mode
  * Added an interactive console for the interactive mode
  * Added packet modification ability in web-driven and interactive modes
  * Added several command-line arguments

#### `==` v0.1 (Oct 07, 2011) `==` ####

  * Very first version of _proxyshark_!
  * Netfilter rules must be written manually:
    * `iptables -I OUTPUT 1 -d 1.2.3.4 -p tcp --dport 80 -j NFQUEUE --queue-num 0`
    * `iptables -I INPUT 1 -s 1.2.3.4 -p tcp --sport 80 -j NFQUEUE --queue-num 0`
  * Packets can be accepted or dropped, but you have to call the functions by yourself
  * Packets are read-only, hmmm not really but there is no API to modify them directly
  * Packets are dissected and printed to stdout by default
  * You must iterate manually over protocols and fields to find a given item
  * No command-line interface
  * No web-based interface
  * Use _-h_ for help