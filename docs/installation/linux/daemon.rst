Daemon
======

:OS: 🐧 *Linux*

To install Desomnia manually as a systemd daemon onto your system, you have several options available.

Prerequisites
-------------

In order to be able to run Desomnia on your system, you will need the .NET Runtime (< 100 MB in size). You can use this official script, to download everything you need into the default location, where the runtime environment will be found automatically::

    curl -sSL https://dot.net/v1/dotnet-install.sh | bash /dev/stdin --channel 10.0 -runtime dotnet --install-dir /usr/share/dotnet

Filesystem layout
-----------------

There is nothing wrong in using Desomnia in portable mode with everything residing in the same directory. But for a persistent installation, you are encouraged to use these locations in alignment with the Filesystem Hierarchy Standard (FHS) on Unix systems:

/usr/sbin`
    Drop the appropriate executable for your platform and architecture into this location, so it can be automatically found. Don't forget to set the necessary executable permission on the file with ``chmod +x /usr/sbin/desomniad``.

/etc/desomnia
    In this location you can put the ``config.xml``, which will tell ARPergefactor how to act. You can also create a ``NLog.config`` file here, to configure additional logging, beside the console output.

/var/log/desomnia
    Here you will find the log files, if you enabled file [[logging]] in the NLog.config and used ``${var:logDir}`` as base path. 

Service configuration
---------------------

In order for automatic restart, we have to ...

/etc/systemd/system/desomnia.service
    If you followed the previous steps, you can create a new systemd service configuration, to have ARPergefactor automatically started every time the system starts:

    .. code:: ini

        [Unit]
        Description=ARPergefactor

        [Service]
        ExecStartPre=find /var/log/arpergefactor/ -type f -delete
        ExecStart=arpergefactor
        Restart=always
        RestartSec=5

        [Install]
        WantedBy=multi-user.target

    You can use that `ExecStartPre` statement, to make sure that the log folder is cleaned every time when the application starts. I used it mostly for debugging purposes.
