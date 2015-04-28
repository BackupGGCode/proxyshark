**THIS PAGE IS UNDER CONSTRUCTION**

**LAST UPDATE: Sep 30, 2013**

This document is based on the current development version of _proxyshark_ and should be considered as a technical specification document. As a consequence, a number of features could be absent from the last stable release and marked as `[DEPRECATED]` or `[UPCOMING]` in the following documentation. See [changelog](ChangeLog.md) and [todolist](ToDoList.md) for further details.

First of all, you have to know that _proxyshark_ can be used in 4 different modes:
  * **View mode**: all you have to do here is run _proxyshark_ with the appropriate arguments and watch the result in your favorite shell. You can setup filters and schedule simple actions based on given conditions, but nothing too complicated. For example, you can define an action to automatically replace by a given value all occurrences of a given value in a given field. You can switch to this mode by running _proxyshark_ with the _[--run](#-r_%7C_--run.md)_ argument or by pressing Ctrl-D in interactive mode (see below).
  * **Interactive mode**: this mode is very close to the view mode except that you can use the embedded shell to change settings and manipulate packets at run-time. It gives you complete control over what _proxyshark_ is doing. To switch to interactive mode, just run _proxyshark_ without the _[--run](#-r_%7C_--run.md)_ argument or press Ctrl-C in view mode. Available commands are listed [below](#Interactive_console.md).
  * **Scripting mode** **`[UPCOMING]`**: you can specify a Python script as [last command-line argument](#%5B_%3Cfilename%3E_%5D.md). All commands available in interactive mode are also available here. Note that you will switch to view mode after the script being interpreted.
  * **Web-driven mode**: a bit more complicated, here _proxyshark_ will generate an HTTP POST to its own web service each time a packet is captured. The idea is to send this request through a local web proxy to handle captured packets through a GUI. This way, all powerful tools provided by such proxy will be available (for example, Scanner, Intruder and Repeater from [Burp Suite Pro](http://portswigger.net/)). Of course, you will need an appropriate extension if you don't want to manipulate raw HTTP requests. Fortunately, an extension for Burp Suite Pro will be released with _proxyshark_ **`[UPCOMING]`**. To use this mode, you have either to pass the _[--web-driven](#-w_%7C_--web-driven_%5B%3Cbind-ip%3E%5D%3A%5B%3Cbind-port%3E%5D%3A%5B%26l.md)_ argument or to [enable it](#set_web-driven_%3Coff%7Con%7C0%7C1%3E.md) from the interactive console. Note that web-driven mode can be used in parallel with any other mode.

## Command-line arguments ##

To use _proxyshark_, simply run the _Python_ script _proxyshark.py_.

The following arguments are available (all optional):

#### `-h | --help` ####

> Print a short help describing the available command-line arguments.

#### `-v | --verbose` ####

> Can be specified several times to increase the [verbosity level](#set_verbosity_%3C0%7C1%7C2%7C3%3E.md).

> Default is _on (level 1)_ in view mode and _off (level 0)_ in interactive mode.

#### `-e | --ethernet` ####

> Turn on [Ethernet mode](#set_ethernet_%3Coff%7Con%7C0%7C1%3E.md).

> Default is _off_.

#### `-q | --queue-num <queue-num>` ####

> Set the _netfilter_ [queue](#set_queue-num_%3Cqueue-num%3E.md) to use.

> Default is _1234_.

#### `-t | --tshark-dir <tshark-dir>` ####

> Set the location of the _[tshark](#set_tshark-dir_%3Ctshark-dir%3E.md)_ binary to use for packet dissection.

> Default is _./bin/`<`arch`>`_ where _`<`arch`>`_ is the current architecture (_i686_, _x86\_64_, etc).

#### `-w | --web-driven [<bind-ip>]:[<bind-port>]:[<proxy-ip>]:[<proxy-port>]` ####

> Turn on [web-driven mode](#set_web-driven_%3Coff%7Con%7C0%7C1%3E.md).

> Default is _off_. If enabled, default parameters are:
    * _`<bind-ip>`_ = _127.0.0.1_
    * _`<bind-port>`_ = _1234_
    * _`<proxy-ip>`_ = _127.0.0.1_
    * _`<proxy-port>`_ = _8080_

> Note that all of them are optional and that _:::_ is allowed.

#### `-c | --capture-filter <capture-filter>` ####

> Set the [capture filter](#set_capture_filter_%3Ccapture-filter%3E.md).

> Default is no filter (or _any_, or _ip_).

#### `-p | --packet-filter <packet-filter>` ####

> Set the [packet filter](#set_packet_filter_%3Cpacket-filter%3E.md).

> Default is no filter (or _any_, or _ip_).

#### `-f | --field-filter <field-filter>` ####

> Set the [field filter](#set_field_filter_%3Cfield-filter%3E.md) (only available in web-driven mode).

> Default is no filter (or _.`*`_).

#### `-r | --run` ####

> Automatically run capture at start, then switch to view mode.

> Default is _off_.

#### `-b | --breakpoint <packet-filter>` ####

> Define a default [breakpoint](#b%7Cbreakpoint_%5B%3Cbreakpoint-id%3E%5D_%5B%3Cpacket-filter%3E%5D.md) based on the given packet filter.

> Default is no breakpoint.

#### `-a | --action <expression>` ####

> An [action](#a%7Caction_%5B%3Caction-id%3E%5D_%5B%3Cbreakpoint-id%3E_%3Cpython-e.md) to run when the default breakpoint is reached.

> Default is no action.

#### [`<filename>`] ####

> An optional script to run at start (in scripting mode) **`[UPCOMING]`**.

> Default is no script.

#### Examples ####

> Run _proxyshark_ in interactive mode:

```
python proxyshark.py --verbose
```

> Capture SIP traffic in web-driven mode with local web proxy running on port 8888 (view mode):

```
python proxyshark.py -v --capture-filter 'port 5060' --packet-filter 'sip' --web-driven :::8888 --run
```

> Replace all HTTP responses by HTTP 500 errors (view mode):

```
python proxyshark.py -v -c 'port 80' -p 'http.response.code' -r --breakpoint 'any' --action 'http.response.code=500;continue'
```

## Interactive console ##

Several commands are available:

#### `h|help [command]` ####

> Print a short help describing the available commands.

#### `i|info [parameter]` ####

> Print information about the current program state:
    * Verbosity level
    * Ethernet mode
    * _Netfilter_ queue
    * _Tshark_ directory
    * Web-driven mode
    * Filters
    * Breakpoints
    * Actions
    * Cache

> You can specify an optional parameter to get information about it. Available parameters are:
    * `verbosity`
    * `ethernet`
    * `queue-num`
    * `tshark-dir`
    * `web-driven`
    * `bind ip`
    * `bind port`
    * `proxy ip`
    * `proxy port`
    * `capture filter`
    * `packet filter`
    * `field filter`
    * `breakpoints`
    * `actions`
    * `cache`

#### `set verbosity <0|1|2|3>` ####

> Set the verbosity level to one of the following values:
    * `0` for errors only (quiet mode)
    * `1` for information and warnings
    * `2` for debug
    * `3` for debug, raw data and XML dissection

#### `set ethernet <off|on|0|1>` ####

> Enable or disable Ethernet mode.

> If enabled, an Ethernet layer will be automatically generated for all captured packets. Otherwise, packets will start at layer 3 (IP). Enabling this mode is required only if you plan to replay packets at layer 2.

#### `set queue-num <queue-num>` ####

> Set the _netfilter_ queue to use.

> This option specifies which queue to use and to send the queue'd data to. The queue number is a 16 bit unsigned integer, which means it can take any value between 0 and 65535.

#### `set tshark-dir <tshark-dir>` ####

> Set the location of the _tshark_ binary to use for packet dissection.

> If not found, _tshark_ is taken from _$PATH_.

#### `set web-driven <off|on|0|1>` ####

> Enable or disable web-driven mode.

> In this mode, an embedded web server will wait for incoming requests from _proxyshark_ itself. The idea is to ask _proxyshark_ to call this web service each time a packet is captured so that we can use a tool such as Burp Suite Pro to handle it.

#### `set bind ip <bind-ip>` | `set bind port <bind-port>` | `set proxy ip <proxy-ip>` | `set proxy port <proxy-port>` ####

> Set parameters of the web-driven mode. The available parameters are:
    * _`<`bind-ip`>`_: binding address of the embedded web server
    * _`<`bind-port`>`_: listening port of the embedded web server
    * _`<`proxy-ip`>`_: IP address of the web proxy to use
    * _`<`proxy-port`>`_: port of the web proxy to use

#### `set capture filter <capture-filter>` ####

> Set the current capture filter.

> This filter acts at a _netfilter_ level to select which packets have to be captured. Basically, you just have to provide a [BPF filter](http://en.wikipedia.org/wiki/Berkeley_Packet_Filter) and _proxyshark_ will use it to generate appropriate _iptables_ rules targeting the _NFQUEUE_ target.

> The formal grammar is:
    * `CaptureFilter` => `[`**any** | `BooleanKeyword]`
    * `BooleanKeyword` => **not** `Keyword` | `Keyword` **and** `Keyword` | `Keyword` **or** `Keyword`
    * `Keyword` => `Device` | `Host` | `Network` | `Port` | `Protocol`
    * `Device` => `[`**in** | **out**`]` **dev** `BooleanValue`
    * `Host` => `[`**src** | **dst**`]` **host** `BooleanValue`
    * `Network` => `[`**src** | **dst**`]` **net** `BooleanValue`
    * `Port` => `[`**src** | **dst**`]` **port** `BooleanValue`
    * `Protocol` => **ip** | **icmp** | **tcp** `[Port]` | **udp** `[Port]`
    * `BooleanValue` => **not** `<`value`>` | `<`value`>` **and** `<`value`>` | `<`value`>` **or** `<`value`>`

> Where values can be:
    * `DeviceValue` => `[`_alphas_`][`_alphanums_-.`_]*`
    * `HostValue` => `IpAddress` | `[`_alphas_`][`_alphanums_-.`_]*`
    * `NetworkValue` => `IpAddress` **/** ( `IpAddress` | `[`_nums_`]{`1,2`}` ) | `IpAddress` **netmask** `IpAddress`
    * `PortValue` => `[`_nums_`]{`1,5`}`

> With:
    * `IpAddress` => `[`_nums_`]{`1,3`}` **.** `[`_nums_`]{`1,3`}` **.** `[`_nums_`]{`1,3`}` **.** `[`_nums_`]{`1,3`}`

##### Examples #####

> Capture _Google Docs_ traffic:

```
host docs.google.com and tcp port 80 or 443
```

```
[DEBUG] name 'docs.google.com' resolved:
[DEBUG] - 173.194.40.134
[DEBUG] - 173.194.40.129
[DEBUG] - 173.194.40.130
[DEBUG] - 173.194.40.135
[DEBUG] - 173.194.40.131
[DEBUG] - 173.194.40.137
[DEBUG] - 173.194.40.128
[DEBUG] - 173.194.40.136
[DEBUG] - 173.194.40.133
[DEBUG] - 173.194.40.142
[DEBUG] - 173.194.40.132
[DEBUG] iptables -t filter -A PROXYSHARK5022 -j NFQUEUE --queue-num 1234
[DEBUG] iptables -t filter -A PROXYSHARK6242 -p tcp --dport 443 -j PROXYSHARK5022
[DEBUG] iptables -t filter -A PROXYSHARK6242 -p tcp --sport 443 -j PROXYSHARK5022
[DEBUG] iptables -t filter -A PROXYSHARK6242 -p tcp --dport 80 -j PROXYSHARK5022
[DEBUG] iptables -t filter -A PROXYSHARK6242 -p tcp --sport 80 -j PROXYSHARK5022
[DEBUG] iptables -t filter -A PROXYSHARK9104 -d 173.194.40.132 -j PROXYSHARK6242
[DEBUG] iptables -t filter -A PROXYSHARK9104 -s 173.194.40.132 -j PROXYSHARK6242
[DEBUG] iptables -t filter -A PROXYSHARK9104 -d 173.194.40.142 -j PROXYSHARK6242
[DEBUG] iptables -t filter -A PROXYSHARK9104 -s 173.194.40.142 -j PROXYSHARK6242
[DEBUG] iptables -t filter -A PROXYSHARK9104 -d 173.194.40.133 -j PROXYSHARK6242
[DEBUG] iptables -t filter -A PROXYSHARK9104 -s 173.194.40.133 -j PROXYSHARK6242
[DEBUG] iptables -t filter -A PROXYSHARK9104 -d 173.194.40.136 -j PROXYSHARK6242
[DEBUG] iptables -t filter -A PROXYSHARK9104 -s 173.194.40.136 -j PROXYSHARK6242
[DEBUG] iptables -t filter -A PROXYSHARK9104 -d 173.194.40.128 -j PROXYSHARK6242
[DEBUG] iptables -t filter -A PROXYSHARK9104 -s 173.194.40.128 -j PROXYSHARK6242
[DEBUG] iptables -t filter -A PROXYSHARK9104 -d 173.194.40.137 -j PROXYSHARK6242
[DEBUG] iptables -t filter -A PROXYSHARK9104 -s 173.194.40.137 -j PROXYSHARK6242
[DEBUG] iptables -t filter -A PROXYSHARK9104 -d 173.194.40.131 -j PROXYSHARK6242
[DEBUG] iptables -t filter -A PROXYSHARK9104 -s 173.194.40.131 -j PROXYSHARK6242
[DEBUG] iptables -t filter -A PROXYSHARK9104 -d 173.194.40.135 -j PROXYSHARK6242
[DEBUG] iptables -t filter -A PROXYSHARK9104 -s 173.194.40.135 -j PROXYSHARK6242
[DEBUG] iptables -t filter -A PROXYSHARK9104 -d 173.194.40.130 -j PROXYSHARK6242
[DEBUG] iptables -t filter -A PROXYSHARK9104 -s 173.194.40.130 -j PROXYSHARK6242
[DEBUG] iptables -t filter -A PROXYSHARK9104 -d 173.194.40.129 -j PROXYSHARK6242
[DEBUG] iptables -t filter -A PROXYSHARK9104 -s 173.194.40.129 -j PROXYSHARK6242
[DEBUG] iptables -t filter -A PROXYSHARK9104 -d 173.194.40.134 -j PROXYSHARK6242
[DEBUG] iptables -t filter -A PROXYSHARK9104 -s 173.194.40.134 -j PROXYSHARK6242
[DEBUG] iptables -t filter -I INPUT 1 -j PROXYSHARK9104
[DEBUG] iptables -t filter -I OUTPUT 1 -j PROXYSHARK9104
[DEBUG] iptables -t filter -I FORWARD 1 -j PROXYSHARK9104
```

> Capture all DNS traffic, and SIP requests from _10.0.0.0/8_ to _1.2.3.4_ or _2.3.4.5_ on network device _eth1_:

```
dev eth1 and (udp port 53 or dst port 5060 and src net 10.0.0.0/8 and dst host 1.2.3.4 or 2.3.4.5)
```

```
[DEBUG] iptables -t filter -A PROXYSHARK5605 -j NFQUEUE --queue-num 1234
[DEBUG] iptables -t filter -A PROXYSHARK5297 -d 2.3.4.5 -j PROXYSHARK5605
[DEBUG] iptables -t filter -A PROXYSHARK5297 -d 1.2.3.4 -j PROXYSHARK5605
[DEBUG] iptables -t filter -A PROXYSHARK6208 -s 10.0.0.0/8 -j PROXYSHARK5297
[DEBUG] iptables -t filter -A PROXYSHARK3724 -p udp --dport 5060 -j PROXYSHARK6208
[DEBUG] iptables -t filter -A PROXYSHARK3724 -p tcp --dport 5060 -j PROXYSHARK6208
[DEBUG] iptables -t filter -A PROXYSHARK3724 -p udp --dport 53 -j PROXYSHARK5605
[DEBUG] iptables -t filter -A PROXYSHARK3724 -p udp --sport 53 -j PROXYSHARK5605
[DEBUG] iptables -t filter -A PROXYSHARK9821 -o eth1 -j PROXYSHARK3724
[DEBUG] iptables -t filter -A PROXYSHARK9821 -i eth1 -j PROXYSHARK3724
[DEBUG] iptables -t filter -I INPUT 1 -j PROXYSHARK9821
[DEBUG] iptables -t filter -I OUTPUT 1 -j PROXYSHARK9821
[DEBUG] iptables -t filter -I FORWARD 1 -j PROXYSHARK9821
```

#### `set packet filter <packet-filter>` ####

> Set the current packet filter.

> This filter is almost like a _Wireshark_ [display filter](http://wiki.wireshark.org/DisplayFilters). You can use it to select captured packets based on dissection criteria.

> The formal grammar is:
    * `PacketFilter` => `[`**any** | `BooleanCondition]`
    * `BooleanCondition` => **not** `Condition` | `Condition` **and** `Condition` | `Condition` **or** `Condition`
    * `Condition` => `Operand` `[Operator` `Value]`
    * `Operand` => **raw** | `Item` | `[`( **len** | **nb** ) **(** `Item` **)**`]`
    * `Item` => `ItemName` `[`**`[`** `AttributeName` **`]`**`]` `[`**`[`** `SliceKey` **`]`**`]`
    * `ItemName` => `[`_alpha_.`_]`+
    * `AttributeName` => `[`_alpha_`]`+
    * `SliceKey` => `[`-_nums_`]*` **:** `[`-_nums_`]*`
    * `Operator` => **`==`** | **`=`** | **`!=`** | **`^=`** | **`*=`** | **`$=`** | **`<=`** | **`<`** | **`>=`** | **`>`**
    * `Value` => `QuotedValue` | `UnquotedValue`
    * `QuotedValue` => `"[`_printable_`]`+`"` | `'[`_printable_`]`+`'`
    * `UnquotedValue` => `[`_printable\_without\_space_`]`+

> Where operators are:
    * Equals: **`==`** or **`=`**
    * Differs: **`!=`**
    * Begins with: **`^=`**
    * Contains: **`*=`**
    * Ends with: **`$=`**
    * Lower than or equal: **`<=`**
    * Lower than: **`<`**
    * Greater than or equal: **`>=`**
    * Greater than: **`>`**

##### Examples #####

> Select ICMP packets with TTL lower than 32:

```
icmp and ip.ttl < 32
```

> Select packets bigger than 1kB containing raw string `'</html>\r\n'`:

```
len(raw) > 1024 and raw*='</html>\r\n'
```

> Select HTTP requests with URI starting with _/index.php?page=_ and User-Agent shorter than 10 characters:

```
http.request.method=GET and http.request.uri^=/index.php?page= and len(http.user_agent) < 10
```

#### `set field filter <field-filter>` ####

> Set the current field filter.

> This filter is only available in web-driven mode. It's just a [regular expression](http://docs.python.org/2/howto/regex.html) to select which protocols and fields are sent to the web proxy (ie which ones will be editable/repeatable through the GUI).

> Note that if no **`^`** or **`$`** characters are found, they will be automatically added at the beginning or at the end of the filter.

##### Examples #####

> Forward only the fields from the SIP layer:

```
sip.*
```

> Forward IP and TCP protocols (not the fields), and all the fields from the HTTP layer:

```
ip$|tcp$|http.*
```

#### `b|breakpoint [<breakpoint-id>] [<packet-filter>]` ####

> Breakpoints are designed to pause the current capture when a given [packet filter](#set_capture_filter_%3Ccapture-filter%3E.md) is triggered. In view mode, you first need to define an [action](#a%7Caction_%5B%3Caction-id%3E%5D_%5B%3Cbreakpoint-id%3E_%3Cpython-e.md) to be run when the breakpoint is triggered (see below). In interactive mode, you can either define an action or alter the packet manually, accept it, drop it, replay it, etc.

> This command can be used in 3 different ways:
    * If no argument is given, print a list of all existing breakpoints (equivalent to [info breakpoints](#i%7Cinfo_%5Bparameter%5D.md)).
    * If a breakpoint identifier is given, print the packet filter associated to this breakpoint. The breakpoint identifier must be an arbitrary string containing letters, digits, dashes, dots or underscores.
    * If a packet filter is given, create a new breakpoint based on the given identifier and filter.

#### `en|enable <breakpoint-id>` ####

> Enable an existing breakpoint.

#### `dis|disable <breakpoint-id>` ####

> Disable an existing breakpoint.

#### `d|delete breakpoint <breakpoint-id>` ####

> Delete an existing breakpoint.

#### `a|action [<action-id>] [<breakpoint-id> <python-expression>]` ####

> Actions are Python expressions to be run when a breakpoint is triggered. Note that commands available in interactive mode are also available in such expressions.

> This command can be used in 3 different ways:
    * If no argument is given, print a list of all existing actions (equivalent to [info actions](#i%7Cinfo_%5Bparameter%5D.md)).
    * If an action identifier is given, print the breakpoint and the Python expression associated to this action. The action identifier must be an arbitrary string containing letters, digits, dashes, dots or underscores.
    * If a breakpoint identifier and a Python expression are given, create a new action based on the given expression and identifiers.

#### `d|delete action <action-id> [<breakpoint-id>]` ####

> Delete an existing action.

> If a breakpoint identifier is given, delete only the association between the action and the breakpoint.

#### `r|run [<capture-filter>] [<packet-filter>] [<field-filter>]` ####

> Run a new capture (drop previously captured packets).

> Filters can be provided to override current [settings](#set_capture_filter_%3Ccapture-filter%3E.md).

> You can also run a capture at start with the _[--run](#-r_%7C_--run.md)_ command-line argument.

#### `p|pause` ####

> Pause the current capture.

#### `c|continue` ####

> Continue the current capture.

#### `s|stop` ####

> Stop the current capture (drop previously captured packets).

#### `q|queue` ####

> Return a list of captured packets.

##### Examples #####

> Get HTTP packets from _1.2.3.4_:

```
>>> packets = queue['ip.src=1.2.3.4 and http']
```

> Retrieve all unique User-Agents from the previous packet list:

```
>>> uniq = lambda x: list(set(reduce(list.__add__, x))) # list(set(...)) is a Python trick to remove doubles
>>> uniq(packets['http.user_agent[show]']
```

#### `p|packet` ####

> A special variable containing the last packet (equivalent to `queue[-1]`).

> You can use it to retrieve the current packet when a breakpoint is triggered in interactive mode.

##### Examples #####

> After a break on an HTTP packet, change the User-Agent and accept the packet:

```
>>> packet['http.user_agent'] = 'User-Agent: My Custom User-Agent\r\n'
>>> packet.accept()
```

> Or drop the current packet and replay it with another server as destination:

```
>>> packet.drop()
>>> packet['ip.dst'] = socket.inet_aton('2.3.4.5')
>>> packet['http.host'] = 'Host: 2.3.4.5\r\n'
>>> packet.replay('http') # see help(DissectedPacket)
```

#### `flush` ####

> Flush internal cache to free memory (captured packets are not removed).

#### `x|exit` ####

> Stop the current capture and quit _proxyshark_.