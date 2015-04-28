**LAST UPDATE: Sep 30, 2013**

#### `==` v1.0b `==` ####

  * Check behavior of _-t_ argument (tshark path)
  * Rename parameters of _-w_ argument (web-driven mode)
  * Implement an option to disable the web-driven mode
  * Make the arguments of the web-driven mode optional

  * Implement _-c_, _-p_ and _-f_ arguments (filters)
  * Implement ability to modify filters dynamically

  * Implement _-r_ argument (run capture at start)
  * Start in interactive mode by default, with no filter and no capture

  * Implement _nfqueue.run_, _.pause_, _.stop_ and _.flush_ methods

  * Implement Ethernet mode
  * Implement _-e_ argument (Ethernet mode)

  * Implement actions:
    * A dict with a _breakpoint_ as key and an _action_ as value
    * Contains a Python expression and an activation flag
    * Call the appropriate action when a breakpoint is triggered
    * Add methods _nfqueue.add\_action_ and _.delete\_action_
  * Implement _-a_ argument (default action)

  * Implement breakpoints:
    * A list of packet filters with an activation flag
    * Test all enabled filters on each captured packet
    * If triggered, call _nfqueue.pause_ and _nfqueue.break_
    * Add methods _nfqueue.add\_breakpoint_, _.enable\_breakpoint_, _.disable\_breakpoint_, _.delete\_breakpoint_
  * Implement _-b_ argument (default breakpoint)

  * Implement packet replay at arbitrary levels (2, 3 and so on):
    * Store hashes of replayed packets (_id_ of the dissected packet)
    * If a packet comes from _lo_ with a known hash, set the _packet.replayed_ flag and store the stream number
    * If a packet comes from a known stream, set the flag too
    * Add _.replayed_ attribute to _DissectedPacket_ and _DissectedPacketList_
    * Add method _DissectedPacket.replay_

  * Restrict autocompletion to a list of predefined commands
  * Make sure that all documented commands are implemented:
    * Including a feature to flush global cache
    * Make available _struct.pack_ and _.unpack_
    * Make available _binascii.a2b\_hex_ and _.b2a\_hex_
    * Make available a function called _uniq_ to remove doubles from a list

#### `==` v2.0 `==` ####

  * Plugin for Burp Suite Pro
  * Implement script name as last argument
  * Implement scripting mode