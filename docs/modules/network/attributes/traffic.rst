minTraffic
++++++++++

When using Desomnia as a :doc:`local sleep manager </guides/sleep>`, you can control the cumulative traffic threshold under which the node will be considered idle. The following formats are valid:

``10``
  A number specified without any unit declares an amount of raw packets that have to be counted during the configured timeout of the :doc:`/modules/system/monitor`, otherwise the node will be considered idle.

``100/s``
  A number specified with a time period but without a binary unit declares an amount of raw packets that have to be counted **in average** during the configured timeout of the :doc:`/modules/system/monitor`, otherwise the node will be considered idle.

``1MB/s``
  A number specified with both a time period and a binary unit declares an amount of bytes that have to be counted **in average** during the configured timeout of the :doc:`/modules/system/monitor`, otherwise the node will be considered idle.

``100kb``
  A number specified without a time period, but with a binary unit declares an amount of bytes that have to be counted during the configured timeout of the :doc:`/modules/system/monitor`, otherwise the node will be considered idle.
