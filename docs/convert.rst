Reading through the [[Configuration walkthrough]] you may have wondered, if ARPergefactor can actually handle IPv6 traffic. It may be shocking for you to hear, but since we're living in the 21th century, of course it can! But since IPv6 addresses are somewhat bulkier to handle, we will dedicate an own little chapter for this. Also, as a matter of fact, im some networks you won't notice any difference, even if you don't configure it. Hope you still find it an interesting read.

Notation
--------

It probably won't surprise you much, that everywhere where you can specify an IPv4 address, you can also declare a corresponding IPv6 address.

.. code:: xml

   <Network interface="eth0">
     <WatchHost name="morpheus" MAC="00:1A:2B:3C:4D:5E" IPv4="192.168.178.10" IPv6="2001:db8:3333:4444:5555:6666:7777:8888" />
   </Network>

But keeping in mind the quite ephemeral nature of IPv6 addresses, this will hardly ever be necessary. Contrary to the custom of assigning your hosts each a well crafted static IPv4 address and marvel over their numerology, IPv6 addresses are pulled from a practically endless pool and, given their validity in the scope of the global address space, changed every so often, to avoid them to become your new personal ID.

So in order to support your devices to be waken in response to IPv6 based requests, the addresses will probably be gathered via one of the available [[Auto configuration]] methods. You see, manual configuration of IPv6 addresses is mainly included for symmetry's sake. Please send me an email, if you find that feature actually useful and explain to me (at length) why that's so.

Behind the scenes
-----------------

Analog to `ARP <https://en.wikipedia.org/wiki/Address_Resolution_Protocol>`__ for resolving IPv4 addresses to their corresponding MAC addresses, there is `NDP <https://en.wikipedia.org/wiki/Neighbor_Discovery_Protocol>`__ for resolving IPv6 addresses. Besides being quite a bit more sophisticated than it's predecessor, really nothing much changed. Thankfully, network nodes still don't complain when the MAC address of another node changes rapidly, on occasions some hundred times an hour. There are of course some attempts to secure NDP, but you probably won't find them anywhere near consumer devices, anytime soon. The simple fact is the following: basically you will have to configure your IP to MAC address mappings statically on every switch and router on the network, to successfully mitigate these kind of "attack" vectors. Beyond that each application sending sensitive information over any kind of network needs to protect itself from man-in-the-middle attacks anyway, which is why we use HTTPS nowadays on a per default basis. So the probability of such attacks actually being successful is quite low in the first place. Good for us, so nobody will get suspicious about what we're doing for the foreseeable future, either.
