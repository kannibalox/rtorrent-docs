Commands Reference
------------------

The reference chapter lists all relevant XMLRPC and ‘private’ commands
provided by *rTorrent* with a short explanation.  See the
:doc:`scripting` on how to combine them into meaningful command
sequences, and :ref:`xmlrpc-api` for some general hints on using the
XMLRPC API

**Use the search box** in the sidebar to find specific commands, or
the :ref:`generated index <genindex>` also lists all the command
names.

The following are similar, but incomplete resources:

-  `PyroScope's reference`_
-  `Fandom.com Reference`_
-  `rTorrent Github Wiki Reference`_

.. _PyroScope's reference: https://github.com/pyroscope/pyroscope/blob/wiki/RtXmlRpcReference.md
.. _Fandom.com Reference: https://scratchpad.fandom.com/wiki/RTorrentCommands
.. _rTorrent Github Wiki Reference: https://github.com/rakshasa/rtorrent/wiki/Scripting-Guide

Format
^^^^^^

All commands will appear in the following format:

.. glossary::

   command_name

        .. code-block:: rtorrentrc

            command_name = type ‹argument› ≫ type ‹returned value›

        All commands are formatted with a similar syntax as they would
        appear in ``rtorrent.rc``. This section would normally contain
        a description of what the command does, along with any
        examples or references.

.. important::

    All commands take a first argument, called ``target``
    internally. This argument is given implicitly in ``rtorrent.rc``
    and the kbd:`Ctrl-X` prompt, but must be given explicitly when
    using the XML-RPC API.

    All of the ``d.*``, ``f.*`` and ``p.*`` commands must have a valid
    target, the format of which is documented at the top of their
    sections. Commands that require a target to be supplied, even if
    implicitly, will have it documented as an argument.  Any commands
    that don't require a valid target should give an empty string as
    their first argument instead.


    ``rtorrent.rc`` format:
      .. code-block:: rtorrentrc

         d.name=
         d.multicall2=main,d.start=
         directory.default.set=/var/data/

    ``XML-RPC`` format:
      .. code-block:: rtorrentrc

         rtxmlrpc d.name DDEE5CB75C12F3165EF79A12A5CD6158BEF029AD
         rtxmlrpc d.multicall2 '' main d.start=
         rtxmlrpc directory.default.set '' /var/data/


Download Items and Attributes
-----------------------------

.. include:: include-cmd-items.rst


Scripting
---------

.. include:: include-cmd-scripting.rst


Logging, Files, and OS
----------------------

.. include:: include-cmd-system.rst


Network (Sockets, HTTP, XMLRPC)
-------------------------------

.. include:: include-cmd-network.rst


Bittorrent Protocol
-------------------

.. include:: include-cmd-bt.rst


User Interface
--------------

.. include:: include-cmd-ui.rst


Miscellaneous
-------------

.. include:: include-cmd-misc.rst


TODO (Groups)
^^^^^^^^^^^^^

 * choke_group
 * file
 * group
 * group2
 * keys
 * ratio
 * scheduler


.. glossary::

    directory.default
    directory.default.set
    directory

        .. code-block:: rtorrentrc

            directory = string ‹path› ≫ 0
            directory.default.set = string ‹path› ≫ 0
            directory.default ≫ string ‹path›

        Sets/gets the default ``d.directory`` for new
        torrents. ``directory`` is an alias for
        ``directory.default.set``.


.. glossary::

    trackers.disable
    trackers.enable

        .. code-block:: rtorrentrc

            trackers.disable = ‹arg› ≫ 0
            trackers.enable = ‹arg› ≫ 0

        Respectively enables or disables trackers across all
        downloads. While an argument is required, it is ignored. If
        :term:`trackers.use_udp` is false, UDP trackers will not be
        enabled.

    trackers.numwant
    trackers.numwant.set

        .. code-block:: rtorrentrc

            trackers.numwant ≫ value ‹numwant›
            trackers.numwant.set = value ‹numwant› ≫ 0

        Sets the default value for :term:`d.tracker_numwant`. Defaults
        to ``-1``.

    trackers.use_udp
    trackers.use_udp.set

        .. code-block:: rtorrentrc

            trackers.use_udp ≫  bool (0 or 1)
            trackers.use_udp.set = bool (0 or 1) ≫ 0

        If set to false, newly added torrents will have all UDP
        trackers set to disabled. It's still possible to manually
        enable them afterwards.

    trackers.alias.items

        .. versionadded:: rTorrent-PS-1.1+

        Returns all the mappings in the form ``‹domain›=‹alias›`` as a list.

        Note that domains that were not explicitly defined so far, but shown
        previously, are also contained in the list, with an empty alias. So to
        create a list for you to fill in the aliases, scroll through all your
        items on ``main`` or ``trackers``, so you can dump the domains of all
        loaded items.

        Example that prints all the domains and their aliases as commands that
        define them:

        .. code-block:: shell

            rtxmlrpc trackers.alias.items \
                | sed -r -e 's/=/, "/' -e 's/^/trackers.alias.set_key = /' -e 's/$/"/' \
                | tee ~/rtorrent/rtorrent.d/tracker-aliases.rc

        This also dumps them into the ``tracker-aliases.rc`` file to persist
        your mappings, and also make them easily editable. To reload edited
        alias definitions, use this:

        .. code-block:: shell

            rtxmlrpc "try_import=,~/rtorrent/rtorrent.d/tracker-aliases.rc"


    trackers.alias.set_key

        Sets an alias that replaces the given domain, when displayed on the
        right of the collapsed canvas.

        .. code-block:: rtorrentrc

            trackers.alias.set_key = bttracker.debian.org, Debian


TODO (singles)
^^^^^^^^^^^^^^

.. glossary::

    print
    add_peer
    catch
    check_hash
    connection_leech
    connection_seed
    download_rate
    encoding.add
    encoding_list
    key_layout
    max_downloads
    max_downloads_div
    max_downloads_global
    max_memory_usage
    max_peers
    max_peers_seed
    max_uploads
    max_uploads_div
    max_uploads_global
    min_downloads
    min_peers
    min_peers_seed
    min_uploads
    on_ratio
    proxy_address
    upload_rate
    file.append
    file.prioritize_toc
    keys.layout
    keys.layout.set

        **TODO**

    fi.filename_last
    fi.is_file

        Reserved for internal use in the file list screen. Cannot be
        used via RPC.

.. _intermediate-commands:

‘Intermediate’ Commands
^^^^^^^^^^^^^^^^^^^^^^^

The *intermediate* commands are kept around as aliases for ‘new’ ones
– at least for the time being. Probably best avoided.

Avoiding the *deprecated* commands is a must, these will disappear at some time.

.. glossary::

    method.use_deprecated
    method.use_deprecated.set

        .. code-block:: rtorrentrc

            method.use_deprecated ≫ bool (0 or 1)
            method.use_deprecated.set = ‹0 or 1› ≫ bool ‹current› (0 or 1)

        The default is ``true``.
        The undocumented ``-D`` command line options sets this to ``false``
        with a ``Disabled deprecated commands`` console message.

    method.use_intermediate
    method.use_intermediate.set

        .. code-block:: rtorrentrc

            method.use_intermediate ≫ value (0 … 2)
            method.use_intermediate.set = ‹0 … 2› ≫ value ‹current› (0 … 2)

        The default is 1 (allow everywhere), values other than 1 or 2 are treated like 0.
        The undocumented ``-I`` command line options sets this to 0
        with a ``Disabled intermediate commands`` console message,
        while ``-K`` sets it to 2,
        printing ``Allowing intermediate commands without xmlrpc``.


All the command aliases can be found in these three source files:
``command_local.cc``, ``command_throttle.cc``, and ``main.cc``.
Search for ``REDIRECT`` using ``grep``.

These are called *intermediate:*

* ``execute`` → ``execute2`` (ignore both, just use ``execute.throw``)
* ``schedule`` → ``schedule2``
* ``schedule_remove`` → ``schedule_remove2``

* ``group.‹name›.view`` → ``group2.‹name›.view``
* ``group.‹name›.view.set`` → ``group2.‹name›.view.set``
* ``group.‹name›.ratio.min`` → ``group2.‹name›.ratio.min``
* ``group.‹name›.ratio.min.set`` → ``group2.‹name›.ratio.min.set``
* ``group.‹name›.ratio.max`` → ``group2.‹name›.ratio.max``
* ``group.‹name›.ratio.max.set`` → ``group2.‹name›.ratio.max.set``
* ``group.‹name›.ratio.upload`` → ``group2.‹name›.ratio.upload``
* ``group.‹name›.ratio.upload.set`` → ``group2.‹name›.ratio.upload.set``

`rTorrent` 0.9.7 adds some missing ``group.seeding.*`` command aliases.


Standard Configuration Sets
---------------------------

.. include:: include-cmd-stdcfg.rst


.. _`auto-scrape.rc`: https://github.com/pyroscope/pyrocore/blob/master/src/pyrocore/data/config/rtorrent.d/auto-scrape.rc#L1

.. END cmd-ref
