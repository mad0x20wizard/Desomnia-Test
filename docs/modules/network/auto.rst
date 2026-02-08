Auto-configuration
==================

After you learned all the possible ways to manually configure the hosts of your network with static IP address mappings, it's probably the time now to forget everything you learned and start using auto-configuration. Provided your router already manages the mappings by itself and make this information available, for example via DNS, you really have to ask yourself if you should compete for that job.

Prerequisites
-------------

So in order to leave the world of static IPs behind, you first have to check if the desired information is available for us to retrieve.

Windows
^^^^^^^

:OS: 🪟

Open up the command line or a PowerShell window and type ``nslookup morpheus`` (replacing our good old example host with an actual host name of your network). The output should ideally look like the following:

::

   Server:     fritz.box
   Address:    fd82:8399:3213:0123:b2f2:a44d:feta:abcd

   Name:       morpheus.fritz.box
   Addresses:  fd82:8399:3213:0123:2c73:1234:8da2:3882
               2001:0000:A23D:71e0:2c73:8d94:8da2:3882
               fd82:8399:3213:0123:1b8e:5c5b:ea23:9f78
               2001:0000:A23D:71e0:1295:09C0:876A:130B
               192.168.128.10

Linux and macOS
^^^^^^^^^^^^^^^

:OS: 🐧 🍎

Open your favorite terminal emulation and type ``dig A morpheus`` to check for the IPv4 address and ``dig AAAA morpheus`` to get all known IPv6 addresses. The result will hopefully contain something like this for IPv4:

::

   ;; QUESTION SECTION:
   ;morpheus.          IN  A

   ;; ANSWER SECTION:
   morpheus.       9   IN  A   192.168.178.10

   ;; AUTHORITY SECTION:
   morpheus.       9   IN  NS  fritz.box.

   ;; ADDITIONAL SECTION:
   fritz.box.      9   IN  A   192.168.178.1
   fritz.box.      9   IN  AAAA    fd82:8399:3213:0123:b2f2:a44d:feta:abcd
   fritz.box.      9   IN  AAAA    2001:0000:A23D:71e0:b2f2:a44d:feta:abcd

and for the IPv6 mappings:

::

   ;; QUESTION SECTION:
   ;morpheus.          IN  AAAA

   ;; ANSWER SECTION:
   morpheus.       9   IN  AAAA    fd82:8399:3213:0123:2c73:1234:8da2:3882
   morpheus.       9   IN  AAAA    2001:0000:A23D:71e0:2c73:8d94:8da2:3882
   morpheus.       9   IN  AAAA    fd82:8399:3213:0123:1b8e:5c5b:ea23:9f78
   morpheus.       9   IN  AAAA    2001:0000:A23D:71e0:1295:09C0:876A:130B

Interpreting the results
^^^^^^^^^^^^^^^^^^^^^^^^

Ideally you will see one IPv4 address and several IPv6 addresses, if your network supports IPv6 at all. Do not be surprised to see **a lot** of IPv6 addresses – they have different purposes. For example the ones starting with a "2" are global ones. One could directly reach your host from the Internet by that address, if anyone can get a handle on it. The ones starting with an "F" are usually of smaller scope, reachable only from inside your network. Furthermore your hosts will also sometimes reserve additonal of IP addresses, long before the old ones expire, so that it always have an IPv6 address for it to use. Desomnia will use all of them if necessary, because your devices are not picky about which address to use, either. Sometimes they try to connect with eachother by their global IP, on other days the site-local address is the way to go. You never know and advisably shouldn't care.

As your local DNS zone includes the local hosts, we can now let Desomnia gather this information on it's own. If you only see IPv4 addresses here, you will probably also be fine. But if this does not work at all for some reason, you may be stuck with the static IP configuration for now. Feel free to try the next steps regardlessly; maybe your network provides a different way of name resolution (WINS, anybody?) and it does work just the same. However, please do not bother writing issue reports if it does not.

Ditching the static mappings
----------------------------

The configuration format offers you maximum flexibility in configuring your devices. Even after you enabled auto-configuration for a particular host or the whole network, you will still be able to add static addresses to the respective hosts. These will always be included, regardless of what the dynamic queries tell us. To enable automatic IP detection, do the following:

.. code:: xml

   <NetworkMonitor interface="eth0" autoDetect="Router|IPv4|IPv6">
     <Host name="pie" IPv4="192.168.178.5" />

     <RemoteHost name="neo" MAC="00:2B:3C:4D:5E:6F" />

     <RemoteHost name="morpheus" MAC="00:1A:2B:3C:4D:5E" autoDetect="IPv4" />
   </NetworkMonitor>

If you enable auto detection on the network, it will be applied to all hosts, unless they are configured differently. In this example Desomnia will query the DNS for both "neo" and "morpheus" for their respective IPv4 addresses, but only neo will receive the available IPv6 mappings, too (some people may still consider IPv6 as something "new"). The host "pie" will be included in the network map as reference for potential filter rules, with auto configured IPv4 and IPv6 addresses and an additional static IPv4 address, if that for example wouldn't be discoverable by DNS.

.. note::

    Please take notice, that you are still **required** to equip your hosts with their physical addresses. It is likely that your router secretly also knows about that information. However, as there is no standardised way of enquiring about this information (except for ARP and NDP, which unfortunately only work for live hosts), you will need to gather this information yourself.

Router detection
^^^^^^^^^^^^^^^^

Maybe you wonder what's the matter with this ``"Router"`` value in the ``autoDetect`` attribute. It's all about not writing down things you don't have to, really. Desomnia can employ a couple of ways to find out about the acting router on the network, so that you may omit the ``Router`` node in your little network map (in most cases; see :doc:`./vpn`).

IPv6 addresses are not forever
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The chances are good, that you and your router have agreed upon a fixed set of IPv4 addresses for your devices. But there is pratically no chance for this to happen when it comes to IPv6 addresses, because you do not even want to have a static IPv6 address in the scope of the global network. Therefore Desomnia needs to know if and when it should discard previously learned addresses and replace them with fresh ones – especially if you use the IPv6 auto detection.

.. code:: xml

   <SystemMonitor version="1">

     <NetworkMonitor interface="eth0" autoDetect="IPv6" autoRefresh="1h" autoTimeout="5s"> <!-- ... --> </NetworkMonitor>

   </SystemMonitor>

The important attribute here is ``autoRefresh``, which defines the maximum timespan during which the Network Monitor can have expired IP addresses in it's cache, after their hosts replaced them for good. The good thing about IPv6 is, your hosts usually secure themselves a new IPv6 address long before they stop to use the old one, and advertise them both. So there probably is no need to be overly paranoid here.

If for some reason you experience timeouts during name resolution, you can vary the value of ``autoTimeout`` to accomodate for your lagging infrastructure. But in most cases you can omit this attribute and use the default value (which is 5 seconds, by the way). If you omit ``autoRefresh`` however, you would have to restart the application to receive any updates on your IP addresses.

Unsolicited advertisements
^^^^^^^^^^^^^^^^^^^^^^^^^^

If you configured a particular host for dynamic detection of an address family, Desomnia will also learn new addresses from broadcasts originating from these devices. This will only work if the host's physical address is known in advance. If we then receive an address advertisement via ARP or NDP, mapping a new IP address to that MAC address, the IP address will be added to the list of known addresses for that host, to further reduce the time frame in which new addresses are unbeknownst to us. These addresses must be confirmed by the local address authority at the next refresh; otherwise, they will be discarded.

MAC address discovery
^^^^^^^^^^^^^^^^^^^^^

Under some circumstances it is possible to discover MAC addresses, so that you wouldn't have to configure them explictily. Theoretically, this can always be achieved through a simple address resolution using ARP or NDP. However, this will fail if the host in question is offline and there is no other agency within your network that can respond on their behalf.

Nonetheless, many consumer and especially business-grade routers will have this information at their disposal. The tricky part is knowing how to use this information. If a public API is available for querying these mappings, you can write a plugin that is called during the Network Monitor's discovery phase to retrieve this information. Integrations for the following routers are already available in the core repository:

- 🚧 FRITZ!Box (planned)

Nevertheless, the Network Monitor will always attempt regular address resolution as a backup if no other source is available.