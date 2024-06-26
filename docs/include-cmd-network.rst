.. _network-commands:

`network.*` commands
^^^^^^^^^^^^^^^^^^^^

.. glossary::

    bind
    network.bind_address
    network.bind_address.set

       .. code-block:: rtorrentrc

           network.bind_address ≫ string ‹address›
           network.bind_address.set = string ‹address› ≫ 0

       Allows binding to a specific interface. This option can only be set at
       startup. ``bind`` is an alias for ``network.bind_address``.

    network.http.dns_cache_timeout
    network.http.dns_cache_timeout.set

        .. code-block:: rtorrentrc

           network.http.dns_cache_timeout ≫  value ‹seconds›
           network.http.dns_cache_timeout.set = value ‹seconds› ≫ 0

        Controls the `DNS cache expiry <https://curl.se/libcurl/c/CURLOPT_DNS_CACHE_TIMEOUT.html>`_
        (in seconds) for HTTP requests. The default is 60 seconds.

        Set to zero to completely disable caching, or set to -1 to
        make the cached entries remain forever.


    network.http.current_open
    network.http.max_open
    network.http.max_open.set

        .. code-block:: rtorrentrc

           network.http.current_open ≫ value ‹num›
           network.http.max_open ≫ value ‹max›
           network.http.max_open.set = value ‹max› ≫ 0

        ``network.http.current_open`` returns the number of currently opened HTTP connections,
        and ``network.http.max_open`` determines the upper limit for simultaneous HTTP connections.

        Be wary of setting this too high, as even if your connection can support
        that many requests, the target host may not be able to respond quickly enough,
        leading to timeouts.


    network.http.proxy_address
    network.http.proxy_address.set

        .. code-block:: rtorrentrc

           network.http.proxy_address ≫  string ‹address›
           network.http.proxy_address.set = string ‹address› ≫ 0

        Controls the HTTP proxy for announcements. See the
        `curl proxy documentation <https://curl.se/docs/manpage.html#-x>`_
        for more information.

        Related: See :term:`network.proxy_address` for proxying peer
        connections.


    network.http.cacert
    network.http.cacert.set
    network.http.capath
    network.http.capath.set

        .. code-block:: rtorrentrc

            network.http.cacert ≫  string ‹file›
            network.http.cacert.set = string ‹file› ≫ 0
            network.http.capath ≫  string ‹dir›
            network.http.capath.set = string ‹dir› ≫ 0

        Allows for controlling which certificates or certificate paths
        are used for validating HTTPS connections. By default the system
        certificates are used, so this option is normally only needed
        for sites that used self-signed or non-standard certificates.

        See the `curl documentation <https://curl.se/docs/manpage.html#--cacert>`_
        for more information.

    network.http.ssl_verify_host
    network.http.ssl_verify_host.set
    network.http.ssl_verify_peer
    network.http.ssl_verify_peer.set

         .. code-block:: rtorrentrc

            network.http.ssl_verify_host ≫ bool (0 or 1)
            network.http.ssl_verify_host.set = bool (0 or 1) ≫ 0
            network.http.ssl_verify_peer ≫ bool (0 or 1)
            network.http.ssl_verify_peer.set = bool (0 or 1) ≫ 0

         Determines if how HTTPS connections are validated.
         By default, both the peer and the host are fully validated.
         See the curl documentation for
         `SSL_VERIFYHOST <https://manpages.ubuntu.com/manpages/jammy/en/man3/CURLOPT_SSL_VERIFYHOST.3.html>`_
         and
         `SSL_VERIFYPEER <https://manpages.ubuntu.com/manpages/jammy/en/man3/CURLOPT_SSL_VERIFYPEER.3.html>`_
         for more information.

    network.listen.backlog
    network.listen.backlog.set

        .. code-block:: rtorrentrc

           network.listen.backlog ≫ value ‹max›
           network.listen.backlog.set = value ‹max› ≫ 0

        Sets the max number of pending TCP connections allowed. This defaults to
        SOMAXCONN, which is 4096 on Linux 5.4+. Note that this is *not* a limit
        on how many peers can actively connect.

    network.listen.port

        .. code-block:: rtorrentrc

           network.listen.port = value ‹port› ≫ 0

        Allows controlling what is reported as the incoming port to the
        tracker. This defaults to whatever port is picked from
        :term:`network.port_range`.

    ip
    network.local_address
    network.local_address.set

        .. code-block:: rtorrentrc

           network.local_address ≫ string ‹address›
           network.local_address.set = string ‹address› ≫ 0

        This allows binding to a specific network interface. By
        default it is blank, meaning it will bind to all network
        interfaces (i.e. ``0.0.0.0``). ``ip`` is an alias for
        ``network.local_address``

    network.max_open_files
    network.max_open_files.set

         .. code-block:: rtorrentrc

            network.max_open_files ≫ value ‹max›
            network.max_open_files.set = value ‹max› ≫ 0

        Controls the max number of open files allowed by the internal file
        manager. By default this is calculated dynamically based on the ``ulimit``.

    network.max_open_sockets
    network.max_open_sockets.set
    network.open_sockets

        .. code-block:: rtorrentrc

           network.max_open_sockets ≫ value ‹max›
           network.max_open_sockets.set = value ‹max› ≫ 0
           network.open_sockets ≫ value ‹active›

        This controls the maximum number of open sockets allowed by
        the internal connection manager. By default this is calculated
        dynamically based on ``ulimit`` settings. ``network.open_sockets`` will return
        the number of actively open sockets controlled by this setting.

    network.port_open
    network.port_open.set
    port_random
    network.port_random
    network.port_random.set
    port_range
    network.port_range
    network.port_range.set

        .. code-block:: rtorrentrc

           network.port_open ≫ bool (0 or 1)
           network.port_open.set = bool (0 or 1) ≫ 0
           network.port_random ≫ bool (0 or 1)
           network.port_random.set = bool (0 or 1) ≫ 0
           network.port_range ≫  string ‹range›
           network.port_range.set = string ‹range› ≫ 0

        This set of options controls how the incoming peer port is
        picked.  ``network.port_open`` allows for disabling the port
        entirely, while ``network.port_range`` defines the range of
        ports rTorrent will consider for use. If
        ``network.port_random`` is false, the range will be scanned in
        sequence until a usable one is found. If no suitable ports are
        found, rTorrent will fail to start.

        ``port_range`` is an alias for
        ``network.port_range.set``. ``port_random`` is an alias for
        ``network.port_random.set``.

    network.proxy_address
    network.proxy_address.set

        .. code-block:: rtorrentrc

           network.proxy_address ≫  string ‹address›
           network.proxy_address.set = string ‹address› ≫ 0

        Controls the proxy for peer connections. This uses the CONNECT
        HTTP 1.0 call to set up the proxied connection, so the target
        must support proxying with that method. If a port is not set
        for the address, it is automatically set to `80`.

        Example:

        .. code-block:: rtorrentrc

           network.proxy_address.set=proxy.example.com:8080

        Note that when setting this to a hostname, a DNS lookup
        happens during the set, and the command only succeeds if the
        DNS lookup succeeds as well.

    network.receive_buffer.size
    network.receive_buffer.size.set
    network.send_buffer.size
    network.send_buffer.size.set

        .. code-block:: rtorrentrc

           network.receive_buffer.size ≫ value ‹size›
           network.receive_buffer.size.set = value ‹size› ≫ 0
           network.send_buffer.size ≫ value ‹size›
           network.send_buffer.size.set = value ‹size› ≫ 0

        Sets or gets the maximum socket receive / send buffer in bytes.

        On Linux, the default buffer size for receiving data is set by the
        ``/proc/sys/net/core/rmem_default`` file (``wmem_default`` for sending).
        The maximum allowed value is set by the ``/proc/sys/net/core/rmem_max`` file
        (``wmem_max`` for sending).

        See the `tuning guide <https://github.com/rakshasa/rtorrent/wiki/Performance-Tuning#networking-tweaks>`_
        for tweaking these values.


    network.scgi.dont_route
    network.scgi.dont_route.set

        .. code-block:: rtorrentrc

           network.scgi.dont_route ≫ bool (0 or 1)
           network.scgi.dont_route.set = bool (0 or 1) ≫ 0

        Enable / disable routing on SCGI connections,
        directly calling `setsockopt <https://linux.die.net/man/3/setsockopt>`_
        to modify the ``SO_DONTROUTE`` flag.


    scgi_local
    network.scgi.open_local
    scgi_port
    network.scgi.open_port

        .. code-block:: rtorrentrc

           network.scgi.open_local = string ‹path› ≫ 0
           network.scgi.open_port = string ‹domain_or_ip:port› ≫ 0

        Open up a Unix domain socket or a TCP port for SCGI communication (i.e. the XMLRPC socket).
        Only use *one* of these!

        .. warning::

            Using ``network.scgi.open_port`` means *any* user on the machine you run *rTorrent* on can
            execute *arbitrary* commands with the permission of the *rTorrent* runtime user.
            Most people don't realize that, now you do! Also, **never** use any other address than
            ``127.0.0.1`` with it.

        ``scgi_local`` is an alias for
        ``network.scgi.open_local``. ``scgi_port`` is an alias for
        ``network.scgi.open_port``.


    network.tos.set

        .. code-block:: rtorrentrc

           network.tos.set = ‹flag› ≫ 0

        Set the `type of service <https://en.wikipedia.org/wiki/Type_of_service>`_
        flag to use in IP packets.

        The options as pulled from :term:`strings.ip_tos` are:

        .. hlist::
            :columns: 3

            * ``default``
            * ``lowdelay``
            * ``throughput``
            * ``reliability``
            * ``mincost``

        ``default`` uses the system default setting.
        A raw hexadecimal value can also be passed in for custom flags.


    network.xmlrpc.dialect.set

        .. code-block:: rtorrentrc

           network.xmlrpc.dialect.set = value ‹dialect› ≫ 0

        Set the XMLRPC dialect to use, as defined by  ``xmlrpc-c``.
        The ``dialect`` parameter can have these values:

        * 0 – ``dialect_generic``
        * 1 – ``dialect_i8``
        * 2 – ``dialect_apache``

        ``dialect_i8`` is the default value, which means the XMLRPC API will use the
        `xmlrpc-c i8 extension type <https://xmlrpc-c.sourceforge.net/doc/libxmlrpc.html#extensiontype>`_
        for returning long integers.

        See `its documentation <https://xmlrpc-c.sourceforge.net/doc/libgeneral.html#dialect>`_
        for more information on how ``xmlrpc-c`` handles dialects.


    network.xmlrpc.size_limit
    network.xmlrpc.size_limit.set

        .. code-block:: rtorrentrc

           network.xmlrpc.size_limit = ≫ value ‹bytes›
           network.xmlrpc.size_limit.set = string ‹max bytes› ≫ 0

        Set or return the maximum size of any XMLRPC requests in bytes.
        Human-readable forms such as ``2M`` are also allowed (for 2 MiB, i.e. 2097152 bytes).


    network.history.auto_scale
    network.history.auto_scale.set
    network.history.depth
    network.history.depth.set
    network.history.refresh
    network.history.sample

        Commands to add network traffic charts to the bottom of the collapsed
        download display.

        Add these lines to your configuration:

        .. code-block:: rtorrentrc

            # rTorrent-PS 0.*+ only!

            # Show traffic of the last hour (112*32 = 3584 ≈ 3600)
            network.history.depth.set = 112

            method.insert = network.history.auto_scale.toggle, simple|private,\
                "branch=(network.history.auto_scale),\
                    ((network.history.auto_scale.set, 0)),\
                    ((network.history.auto_scale.set, 1))"
            method.insert = network.history.auto_scale.ui_toggle, simple|private,\
                "network.history.auto_scale.toggle= ; network.history.refresh="

            schedule2 = network_history_sampling, 1, 32, "network.history.sample="
            schedule2 = bind_auto_scale, 0, 0,\
                "ui.bind_key=download_list, =, network.history.auto_scale.ui_toggle="

        This will add the graph above the footer,
        you get the upper and lower bounds of traffic
        within your configured time window, and each bar of the graph
        represents an interval determined by the sampling schedule.

        Pressing ``=`` toggles between a graph display with base line 0,
        and a zoomed view that scales it to the current bounds.


`ip_tables.*` commands
^^^^^^^^^^^^^^^^^^^^^^^^^^

.. glossary::

    ip_tables.add_address
    ip_tables.get
    ip_tables.insert_table
    ip_tables.size_data

        **TODO**


`ipv4_filter.*` commands
^^^^^^^^^^^^^^^^^^^^^^^^^^

.. glossary::

    ipv4_filter.add_address
    ipv4_filter.dump
    ipv4_filter.get
    ipv4_filter.load
    ipv4_filter.size_data

        **TODO**

.. END cmd-network
