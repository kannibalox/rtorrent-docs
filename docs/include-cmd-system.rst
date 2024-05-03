.. _execute-commands:

`execute.*` commands
^^^^^^^^^^^^^^^^^^^^

Call operating system commands, possibly catching their output for use within *rTorrent*.


.. note::

    The ``.bg`` variants detach the child process from the *rTorrent* parent,
    i.e. it runs in the background. This **must** be used if you want to call
    back into *rTorrent* via XMLRPC, since otherwise there *will* be a deadlock.

    ``throw`` means to raise an error when the called command fails,
    while the ``nothrow`` variants will return the exit code.


.. glossary::

    execute.throw
    execute.throw.bg
    execute2

        .. code-block:: rtorrentrc

            execute.throw[.bg] = string command, string arg1, string arg2, ... ≫ 0

        This will execute a system command with the provided arguments.
        These commands either raise an error or return ``0``.
        ``execute2`` is the same as ``execute.throw``, and should be avoided.

        Since internally ``spawn`` is used to call the OS command, the
        shell is not involved and things like shell redirection will
        not work here.  There is also no reason to use shell quoting
        in arguments, just separate them by commas.  If you need shell
        features, call ``bash -c "‹command›"`` like shown in this
        example:

        .. code-block:: rtorrentrc

            # Write a PID file into the session directory
            execute.throw = sh, -c, (cat, "echo >", (session.path), "rtorrent.pid", " ", (system.pid))

        Note that the result of the ``(cat, …)`` command ends up as a *single* argument passed on to ``bash``.


    execute.nothrow
    execute.nothrow.bg

        .. code-block:: rtorrentrc

            execute.nothrow[.bg] = string command, string arg1, string arg2, ... ≫ value ‹exit status›

        Like :term:`execute.throw`, but return the command's exit code
        (*warning:* due to a bug the return code is shifted by 8 bits,
        so ``1`` becomes ``0x100``).

        The ``.bg`` variant will just indicate whether the child could
        be successfully spawned and detached.


    execute.capture
    execute.capture_nothrow

        .. code-block:: rtorrentrc

            execute.capture[_nothrow] = string command, string arg1, string arg2, ... ≫ string ‹stdout›

        Like ``execute.[no]throw``, but returns the command's standard output.
        The ``nothrow`` variant returns any output that was written before an error,
        in case one occurs. The exit code is never returned.

        Note that any line-endings are included, so if you need a plain string value,
        wrap the command you want to call into an ``echo -n`` command:

        .. code-block:: rtorrentrc

            method.insert = log_stamp, private|simple,\
                "execute.capture_nothrow = bash, -c, \"echo -n $(date +%Y-%m-%d-%H%M%S)\""


    execute.raw
    execute.raw.bg
    execute.raw_nothrow
    execute.raw_nothrow.bg

        The ``execute.raw`` variants function identically to other ``execute.*`` commands,
        except that a tilde in the path to the executable is not expanded.


.. _system-commands:

`system.*` commands
^^^^^^^^^^^^^^^^^^^^^^^^^^

Commands related to the operating system and the XMLRPC API.

.. glossary::

    system.listMethods
    system.methodExist
    system.methodHelp
    system.methodSignature
    system.getCapabilities

        .. code-block:: rtorrentrc

            system.listMethods ≫ list[string] ‹methods›
            system.methodExist = string ‹method› ≫ bool (0 or 1)
            system.methodHelp = string ‹method› ≫ string ‹help›
            system.methodSignature = string ‹method› ≫ string ‹signature›
            system.getCapabilities ≫ list[string] ‹capabilities›

        XML-RPC introspection methods. For more information, see `XML-RPC Introspection`_.
        Note that no help or signature is currently defined for *rTorrent*-specific
        commands.

    system.capabilities

        .. code-block:: rtorrentrc

            system.capabilities ≫ list[string] ‹capabilities›

        This returns protocol and version information about the
        XML-RPC interface implementation.  See
        `xmlrpc-c system.capabilities`_ for more.


    system.multicall

        Similar to :term:`d.multicall2`, this allows multiple commands
        to be sent in one request.  Unlike :term:`d.multicall2`, this
        is a generic multicall not specific to rTorrent.  See the
        `xmlrpc-c system.multicall docs`_ for more.


    system.daemon
    system.daemon.set

        .. versionadded:: 0.9.7

        When set to true, `rTorrent` starts in the background without
        any ``ncurses`` UI.  It can then only be controlled via XMLRPC
        commands and POSIX signals.

        See `Daemon Mode`_ in the `rTorrent` wiki for more.


    system.shutdown.normal
    system.shutdown.quick

        .. versionadded:: 0.9.7

        Shut down `rTorrent` gracefully. The ``normal`` version waits
        for `BitTorrent` stop events to be sent to trackers of all
        active items, while the ``quick`` version does not.

        In older versions of `rTorrent`, the only way to shut down the
        client without using ``^Q`` in the terminal UI is to send a
        signal:

        .. _posix-signals:

        .. rubric:: POSIX Signal Handling

        ``SIGINT`` and ``SIGHUP`` act like ``system.shutdown.normal``,
        while ``SIGTERM`` is equivalent to ``system.shutdown.quick``.
        ``SIGHUP`` support exists in `rTorrent-PS` and since
        `rTorrent` 0.9.7.

        ``SIGWINCH`` causes a forced canvas redraw.

        ``SIGPIPE`` is generally ignored, and ``SIGUSR1`` ‘does
        nothing’.

        ``SIGSEGV``, ``SIGILL``, and ``SIGFPE`` cause a panic, meaning
        things are cleaned up if possible, before exiting the client.
        Also a stack dump is created, if that was enabled during
        compilation.

        ``SIGBUS`` behaves almost the same as ``SIGSEGV`` (exits the
        client), but also prints some additional information regarding
        the signal reason, like *Invalid address alignment* or
        *Non-existent physical address*.


    system.shutdown

        This shuts down the XMLRPC server, but does **not** shut down `rTorrent`.
        It's a built-in of the `xmlrpc-c` library.

        See also :term:`system.shutdown.normal` and  :term:`system.shutdown.quick`.

    system.api_version
    system.client_version
    system.library_version

        .. code-block:: rtorrentrc

            system.api_version ≫ string ‹version›
            system.client_version ≫ string ‹version›
            system.library_version ≫ string ‹version›

        The versions of the XMLRPC API, the `rTorrent` client, and the
        `libtorrent` library respectively.  The client and library
        versions are currently tightly coupled, while
        ``system.api_version`` is incremented whenever changes are
        made to the XMLRPC API.


    system.client_version.as_value

        *New in rTorrent-PS 1.1+ only*

        .. code-block:: rtorrentrc

            system.client_version.as_value ≫ value ‹Mmmpp version decimal›

        Returns the same information as :term:`system.client_version`, but instead of
        a dotted string you get an integer number that can be easily compared in version checks.

        :term:`system.has` should be preferred though to write configuration that
        works across some client version range.

        Example:

        .. code-block:: console

            $ rtxmlrpc --repr system.client_version.as_value
            906
            $ rtxmlrpc --repr system.client_version
            '0.9.6'


    system.colors.enabled
    system.colors.max
    system.colors.rgb

        .. code-block:: rtorrentrc

            # rTorrent-PS only
            system.colors.enabled ≫ bool (0 or 1)
            system.colors.max ≫ value ‹colors›
            system.colors.rgb ≫ value

        Returns some ``ncurses`` system state related to colors (in rTorrent-PS only).

    system.cwd
    system.cwd.set

        .. code-block:: rtorrentrc

            system.cwd ≫ string ‹path›
            system.cwd.set = string ‹path› ≫ 0

        Query or change the current working directory of the running process.
        This will affect any relative paths used after the change, e.g. in schedules.

    system.env

        .. versionadded:: 0.9.7

        *New in rTorrent-PS 0.X*

        .. code-block:: rtorrentrc

            system.env = ‹varname› ≫ string ‹env-value›

        Query the value of an environment variable,
        returns an empty string if ``$varname`` is not defined.

        Example:

        .. code-block:: rtorrentrc

            session.path.set = (cat, (system.env, RTORRENT_HOME), "/.session")


    system.file.allocate
    system.file.allocate.set

        .. code-block:: rtorrentrc

            system.file.allocate ≫ bool (0 or 1)
            system.file.allocate.set = bool (0 or 1) ≫ 0

        Controls whether file pre-allocation is enabled. If it is, and file allocation
        is supported by the file system, the full amount of space required for a file
        is allotted *immediately* when an item is started. Otherwise space is used only
        when data arrives and must be stored.


    system.file.max_size
    system.file.max_size.set

        See :term:`d.max_file_size`.


    system.file.split_size
    system.file.split_size.set
    system.file.split_suffix
    system.file.split_suffix.set

        **TODO**

    system.file_status_cache.prune
    system.file_status_cache.size

        .. code-block:: rtorrentrc

            system.file_status_cache.size ≫ value ‹size›
            system.file_status_cache.prune ≫ 0

        Used when loading metafiles from a directory/glob, this helps prevent *rTorrent* from trying
        to load the same file multiple times, especially when using watch directories.

    system.files.closed_counter
    system.files.failed_counter
    system.files.opened_counter

        .. code-block:: rtorrentrc

            system.files.closed_counter ≫ value ‹closed›
            system.files.failed_counter ≫ value ‹failed›
            system.files.opened_counter ≫ value ‹opened›

        Return the number of files which were closed, failed to open, and were
        successfully opened respectively.

    system.has
    system.has.list

        *New in rTorrent-PS 1.1+ only*

        .. code-block:: rtorrentrc

            system.has = ‹capability› ≫ bool (0 or 1)
            system.has.list = ≫  list[string] ‹capabilities›

        This can be used to write configuration files that work on older builds
        (and on vanilla rTorrent), even when new features and commands are introduced.

        The ``system.has.list`` command returns a list of non-method capabilities
        the running client has on board.

        .. code-block:: console

            $ rtxmlrpc --repr system.has.list
            ['canvas_v2', 'colors', 'rtorrent-ps', 'system.has']

        If a method name (ending in ``=``) is passed, the call returns ``true``
        when that method is already defined.
        The XMLRPC command :term:`system.methodExist` is similar,
        but cannot be used from within `rTorrent` and its configuration.

        .. code-block:: console

            $ rtxmlrpc system.has '' system.has=
            1
            $ rtxmlrpc system.has '' cute.kittens=
            0

        To make sure the ``system.has`` command can be actually used,
        add this somewhere *early* in your configuration:

        .. code-block:: rtorrentrc

            # `system.has` polyfill (the "false=" silences the `catch` command, in rTorrent-PS)
            catch = {"false=", "method.redirect=system.has,false"}

        The following branch somehow self-absorbedly shows how this can be used:

        .. code-block:: rtorrentrc

            branch=(system.has, system.has), ((print, "Your build can haz system.has!"))

        In a vanilla rTorrent, there is silence, and zero capabilities.

        A very practical use-case is auto-detection of *rTorrent-PS 1.1+*:

        .. code-block:: rtorrentrc

            # `system.has` polyfill (the "false=" silences the `catch` command, in rTorrent-PS)
            catch = {"false=", "method.redirect=system.has,false"}

            # Set "pyro.extended" to 1 to activate rTorrent-PS features!
            method.insert = pyro.extended, const|value, (system.has, rtorrent-ps)

        It's also especially useful in combination with a branched import, so whole snippets
        using new features are safe and don't blow up in older builds.
        Or conditionally backfill something missing in older builds, like in this example:

        .. code-block:: rtorrentrc

            # Define default columns for older rTorrent-PS builds
            branch=(not, (system.has, canvas_v2)), ((import, rtorrent.d/05-rt-ps-columns.rc.include))


    system.has.private_methods
    system.has.public_methods

        *New in rTorrent-PS 1.1+ only*

        .. code-block:: rtorrentrc

            system.has.private_methods ≫ list[string] ‹method names›
            system.has.public_methods ≫ list[string] ‹method names›

        The ``system.has.private_methods`` and ``system.has.public_methods`` commands
        return a list of private / public methods, either built-in or defined via configuration.
        *Private* means a method is not callable via XMLRPC.

        ``system.has.public_methods`` returns almost the same result as :term:`system.listMethods`
        – what's missing is the built-in methods of `xmlrpc-c` (see the example below).

        Both these commands are only useful for things like
        comparing the lists generated by different client versions,
        or checking the completeness of this reference.

        .. code-block:: console

            $ rtxmlrpc system.has.private_methods | wc -l
            112
            $ rtxmlrpc system.has.public_methods | wc -l
            818
            $ ( rtxmlrpc system.has.public_methods ; rtxmlrpc system.has.private_methods ) \
              | sort | uniq -c | awk '{print $1}' | sort | uniq -c
                930 1
            $ ( rtxmlrpc system.has.public_methods ; rtxmlrpc system.listMethods ) \
              | sort | uniq -c | egrep -v '^ +2 ' | awk '{print $2}'
            system.capabilities
            system.getCapabilities
            system.listMethods
            system.methodExist
            system.methodHelp
            system.methodSignature
            system.multicall
            system.shutdown


    system.hostname

        .. code-block:: rtorrentrc

            system.hostname ≫ string ‹hostname›

        Returns the hostname of the system.

    system.pid

        .. code-block:: rtorrentrc

            system.pid ≫ value ‹pid›

        Returns the main process ID of the running client.

    system.random

        *New in rTorrent-PS 1.0+ only*

        .. code-block:: rtorrentrc

            system.random = [[‹lower›,] ‹upper›] ≫ value

        Generate *uniformly* distributed random numbers in the range
        defined by ``lower`` … ``upper``.

        The default range with no args is ``0`` … ``RAND_MAX``. Providing
        just one argument sets an *exclusive* upper bound, and two
        args define an *inclusive*  range.

        An example use-case is adding jitter to time values that you
        later check with :term:`elapsed.greater`, to avoid load spikes and
        similar effects of clustered time triggers.

    system.time
    system.time_seconds
    system.time_usec

        .. code-block:: rtorrentrc

            system.time ≫ value ‹time›
            system.time_seconds ≫ value ‹time›
            system.time_usec ≫ value ‹time›

        Returns the system times in `epoch <https://en.wikipedia.org/wiki/Unix_time>`_ notation.
        ``system.time_usec`` returns the value in microseconds instead of seconds.
        ``system.time`` is essentially an alias for ``system.time_seconds``.

        **TODO** Is there any practical difference when using the cached ``system.time``?

    system.umask.set

        .. code-block:: rtorrentrc

            system.umask.set ≫ value ‹time›

        Set the `umask`_ for the running *rTorrent* process.


.. _`umask`: https://en.wikipedia.org/wiki/Umask
.. _`XML-RPC Introspection`: https://xmlrpc-c.sourceforge.net/introspection.html
.. _`xmlrpc-c system.multicall docs`: https://openacs.org/api-doc/proc-view?proc=system.multicall
.. _`xmlrpc-c system.capabilities`: https://xmlrpc-c.sourceforge.net/doc/libxmlrpc_server.html#system.capabilities
.. _`Daemon Mode`: https://github.com/rakshasa/rtorrent/wiki/Daemon_Mode


.. _log-commands:

`log.*` commands
^^^^^^^^^^^^^^^^^^^^^^^^^^

.. glossary::

    log.add_output

        .. code-block:: rtorrentrc

            log.add_output = string ‹scope›, string ‹name› ≫ 0

        This command adds another logging scope to a named log file,
        opened by one of the :term:`log.open_file` commands.

        Log messages are classified into groups
        (``connection``, ``dht``, ``peer``, ``rpc``, ``storage``, ``thread``, ``torrent``, and ``tracker``),
        and have a level of ``critical``, ``error``, ``warn``, ``notice``, ``info``, or ``debug``.

        Scopes can either be a whole level,
        or else a group on a specific level by using ``‹group›_‹level›`` as the scope's name.

        Example:

        .. code-block:: rtorrentrc

            log.add_output = tracker_debug, tracelog


    log.close

        .. versionadded:: 0.9.8

        .. code-block:: rtorrentrc

            log.close = string ‹name› ≫ 0

        Removes all outputs from the named log, and closes the file.
        To ‘close’ a log prior to 0.9.8, bind it to ``/dev/null``.


    log.execute

        .. code-block:: rtorrentrc

            log.execute = string ‹path› ≫ 0

        (Re-)opens a log file that records commands called via :ref:`execute-commands`,
        including their return code and output.
        This can grow large quickly, see :ref:`log-rotation` for how to manage this and other log files.

        Passing an empty string closes the file (but be aware of issue
        `#743 <https://github.com/rakshasa/rtorrent/pull/743>`_, only fixed in 0.9.8+ / PS 1.1).

        Example:

        .. code-block:: rtorrentrc

            log.execute = (cat, (cfg.logs), "execute.log")


    log.xmlrpc

        .. code-block:: rtorrentrc

            log.xmlrpc = string ‹path› ≫ 0

        (Re-)opens a log file that contains a log of commands executed via XMLRPC.
        This logs the raw SCGI and XMLRPC call and response for each request.
        The file can get huge quickly, see :ref:`log-rotation` for how to manage this and other log files.

        Passing an empty string closes the file.

        Example:

        .. code-block:: rtorrentrc

            log.xmlrpc = (cat, (cfg.logs), "xmlrpc.log")

    log.open_file
    log.open_gz_file
    log.open_file_pid
    log.open_gz_file_pid

        .. code-block:: rtorrentrc

            log.open_file = string ‹name›, string ‹log file path›[, string ‹scope›…] ≫ 0
            log.open_gz_file
            log.open_file_pid
            log.open_gz_file_pid

        All these commands open a log file, giving it a name to refer to.
        Paths starting with ``~`` are expanded.
        You can immediately add some logging scopes,
        see :term:`log.add_output` for details on those.

        The ``pid`` variants add the PID of *rTorrent* at the end of the file name
        (see :ref:`log-rotation` for a way better scheme for log separation).
        Adding ``gz`` opens the logfile directly as a compressed streams,
        note that you have to add an appropriate extension yourself.

        There is an arbitrary limit on the number of log streams you can open (64 in 0.9.6).
        The core of the logging subsystem is implemented in ``torrent/utils/log`` of *libtorrent*.

        You can re-open existing logs in *rTorrent-PS* 1.1+ (and maybe in *rTorrent* 0.9.7+),
        by just calling an open command with a new path. To close one, see :term:`log.close`.

        Example:

        .. code-block:: rtorrentrc

            log.open_file_pid = tracker, /tmp/tracker.log, tracker_debug
            # … opens '/tmp/tracker.log.NNNNN' for debugging tracker announces etc.

        .. warning::

            Compressed log files do not seem to work, in version 0.9.6 at least.

    log.vmmap.dump

        .. code-block:: rtorrentrc

            log.vmmap.dump = string ‹dump file path› ≫ 0

        Dumps all memory mapping regions to the given file,
        each line contains a region in the format ``‹begin›-‹end› [‹size in KiB›k]``.


    log.messages

        *rTorrent-PS 0.x+ only*

        .. code-block:: rtorrentrc

            log.messages = string ‹log file path› ≫ 0

        (Re-)opens a log file that contains the messages normally only visible
        on the main panel and via the ``l`` key. Each line is prefixed with the
        current date and time in ISO8601 format. If an empty path is passed, the
        file is closed.

        Example:

        .. code-block:: rtorrentrc

            log.messages = (cat, (cfg.logs), "messages.log")

.. END cmd-system
