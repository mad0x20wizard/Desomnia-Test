pingTimeout
+++++++++++

:inherited:
:default: ``500ms``

Defines the timeout, after which a remote host will be considered unreachable, after a ARP request, NDP solicitation or ICMP echo request remains without reply. Decrease this value to accelerate demand requests in general. Increase this timeout to reduce unnecessarily executed demand requests on a lagging network.

pingFrequency
+++++++++++++

:inherited:

This defines the frequency at which a remote host will be checked to determine whether it is operational. In order to ``advertise`` the changed IP ownership of a remote host early, the respective host has to be checked regularly. If you don't configure this value, **no** regular checks will be performed.