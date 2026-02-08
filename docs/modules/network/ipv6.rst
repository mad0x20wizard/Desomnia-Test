IPv6 support
============

Reading through the :doc:`/guides/wake` guide you may have wondered, if Desomnia can handle IPv6 traffic, too. It may be shocking for you to hear, but since we're living in the 21th century, of course it can! But since IPv6 addresses are somewhat more clumsy to handle, we will dedicate an own little chapter for them. Also, as a matter of fact, in some networks you won't notice any difference, even if you don't configure it. Hope you still find it an interesting read.

Notation
--------

As you might expect, wherever you can specify an IPv4 address, you can also declare a corresponding IPv6 address:

.. code:: xml

  <NetworkMonitor interface="eth0" network="2a02:8071:51e0:71e0::/59">

    <HostFilterRule name="neo" IPv6="2a02:8071:51e0:71e0:c43f:bb3f:b00c:faf5">

    <RemoteHost name="morpheus" MAC="00:1A:2B:3C:4D:5E"
      IPv4="192.168.178.10" 
      IPv6="2a02:8071:51e0:71e0:1048:a52:b322:dc4f" />

    <HostRange name="range" network="2a02:8071:51e0:71e0::/59">

    <HostRange name="range" 
      firstIP="2a02:8071:51e0:71e0:0000:0000:0000:0000"
      lastIP="2a02:8071:51e0:71ff:ffff:ffff:ffff:ffff">

  </NetworkMonitor>

However, bearing in mind the transient nature of IPv6 addresses, this will rarely be necessary. Contrary to the tradition of assigning each of your hosts a well-crafted static IPv4 address and marvelling at their numerology, IPv6 addresses are pulled from a practically endless pool. Given their validity within the scope of the global address space, they are changed periodically to prevent them from becoming your new personal ID.

In order to enable your devices to wake in response to IPv6-based requests, the addresses will most likely be gathered via one of the available :doc:`auto` methods. You see, manual configuration of IPv6 addresses is mainly included for symmetry's sake. Please send me an email, if you find that feature actually useful and explain to me (at length) why that's so.

Behind the scenes
-----------------

Just as `ARP`_ is used to resolve IPv4 addresses to their corresponding MAC addresses, `NDP`_ is used to resolve IPv6 addresses. Besides being quite a bit more sophisticated protocol than it's predecessor, really nothing much changed. Thankfully, network nodes still don't complain when the MAC address of another node changes rapidly, occasionally some hundred times an hour. There are of course some attempts to secure NDP, but you probably won't find them anywhere near consumer devices, anytime soon. The simple fact is the following: basically you will have to configure your IP to MAC address mappings statically on every switch and router on the network, to successfully mitigate these kind of "attack" vectors. Beyond that each application sending sensitive information over any kind of network needs to protect itself from man-in-the-middle attacks anyway, which is why we use HTTPS nowadays on a per default basis. So the probability of such attacks actually being successful in the first place is quite low. That's good for us, as it means nobody will be suspicious of what we're doing for the foreseeable future.

.. _`ARP`: https://en.wikipedia.org/wiki/Address_Resolution_Protocol
.. _`NDP`: https://en.wikipedia.org/wiki/Neighbor_Discovery_Protocol
