Frequently Asked Questions
==========================

This document lists categorized answers and questions with links to the relevant documentation.

Replication
-----------
Please note that not all PowerDNS Server backends support primary or secondary operation, see the :doc:`table of backends <../backends/index>`.

My PowerDNS Authoritative Server does not send NOTIFY messages
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Don't forget to enable primary support by setting :ref:`setting-primary` to ``yes`` in your configuration.
In :ref:`primary mode<primary-operation>` PowerDNS Authoritative Server will send NOTIFYs to all nameservers that are listed as NS records in the zone by default.

My PowerDNS Authoritative Server does not start AXFRs
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Don't forget to enable secondary-support by setting :ref:`setting-secondary` to ``yes`` in your configuration.
In :ref:`secondary mode<secondary-operation>` PowerDNS Authoritative Server listens for NOTIFYs from the primary IP for zones that are configured as secondary zones,
and will also periodically check for SOA serial number changes at the primary.

Can PowerDNS Server act as Secondary and Primary at the same time?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Yes totally, enable both by saying ``yes`` to :ref:`setting-primary` and :ref:`setting-secondary` in your configuration.

How can I limit Zone Transfers (AXFR) per Domain?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
With the ALLOW-AXFR-FROM metadata, See :ref:`the documentation <metadata-allow-axfr-from>`.

I have a working Autoprimary/Autosecondary setup but when I remove Zones from the Primary they still remain on the Secondary. Am I doing something wrong?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
You're not doing anything wrong.
This is the perfectly normal and expected behavior because the AXFR (DNS Zonetransfer) Protocol does not provide for zone deletion.
You need to remove the zones from the secondary manually or via a custom script.

Operational
-----------

The ADDITIONAL is section different than BIND's answer, why?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The PowerDNS Authoritative Server by default does not 'trust' other zones in its own database.

PowerDNS does not give authoritative answers, how come?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
This is almost always not the case.
An authoritative answer is recognized by the 'AA' bit being set.
Many tools prominently print the number of Authority records included in an answer, leading users to conclude that the absence or presence of these records indicates the authority of an answer. This is not the case.

Verily, many misguided country code domain operators have fallen into this trap and demand authority records, even though these are fluff and quite often misleading.
Invite such operators to look at :rfc:`section 6.2.1 of RFC 1034 <1034#section-6.2.1>`, which shows a correct authoritative answer without authority records.
In fact, none of the non-deprecated authoritative answers shown have authority records!

Primary or Secondary support is not working, PowerDNS is not picking up changes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The Primary/Secondary apparatus is off by default.
Turn it on by adding a :ref:`setting-secondary` and/or :ref:`setting-primary` statement to the configuration file.
Also, check that the configured backend is primary or secondary capable and you entered exactly the same string to the Domains tables without the ending dot.

My primaries won't allow PowerDNS to access zones as it is using the wrong local IP address
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
By default, PowerDNS lets the kernel pick the source address.
To set an explicit source address, use the :ref:`setting-query-local-address` setting.

PowerDNS does not answer queries on all my IP addresses (and I've ignored the warning I got about that at startup)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Please don't ignore what PowerDNS says to you.
Furthermore, see the documentation for the :ref:`setting-local-address` and :ref:`setting-local-ipv6` settings, and use it to specify which IP addresses PowerDNS should listen on.
If this is a fail-over address, then the :ref:`setting-local-address-nonexist-fail` and :ref:`setting-local-ipv6-nonexist-fail` settings might interest you.

Linux Netfilter says your conntrack table is full?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Thats a common problem with Netfilter Conntracking and DNS Servers, just tune your kernel variable (``/etc/sysctl.conf``) ``net.ipv4.netfilter.ip_conntrack_max`` up accordingly.
Try setting it for a million if you don't mind spending some MB of RAM on it for example.

I get an error about writing to /etc
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This may look something like "unable to open temporary zonefile '/etc/powerdns/zones/example.com.<random number>'".
PowerDNS systemd units enable ``ProtectSystem=full`` by default, which disallows writes to ``/etc`` and ``/usr``, among other places.
Either move your zone files to a safer place (``/var/lib/powerdns`` is a popular choice) or change the systemd protection settings.

For more background on this, please see the systemd documentation on `ProtectSystem <https://www.freedesktop.org/software/systemd/man/systemd.exec.html#ProtectSystem=>`_ and `ReadWritePaths <https://www.freedesktop.org/software/systemd/man/systemd.exec.html#ReadWritePaths=>`_.

Backends
--------

Does PowerDNS support splitting of TXT records (multipart or multiline) with the MySQL backend?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
PowerDNS with the :doc:`../backends/generic-sql` do NOT support this.
Simply make the "content" field in your database the appropriate size for the records you require.

I see this a lot of "Failed to execute mysql_query" or similar log-entries
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Check your MySQL timeout, it may be set too low.
This can be changed in the ``my.cnf`` file.

Which backend should I use? There are so many!
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
If you have no external constraints, the :doc:`../backends/generic-mysql`, :doc:`../backends/generic-postgresql` and :doc:`../backends/generic-sqlite3` ones are probably the most used and complete.

The bindbackend is also pretty capable too in fact, but many prefer a relational database.

Can I launch multiple backends simultaneously?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
You can.
This might for example be useful to keep an existing BIND configuration around but to store new zones in, say MySQL.
The syntax to use is ``launch=bind,gmysql``.
Do note that multi-backend behaviour is not specified and might change between versions.
This is especially true when DNSSEC is involved.

I've added extra fields to the domains and/or records table. Will this eventually affect the resolution process in any way?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
No, the :doc:`../backends/generic-sql` use several default queries to provide the PowerDNS Server with data and all of those refer to specific field names, so as long as you don't change any of the predefined field names you are fine.

Can I specify custom sql queries for the gmysql / gpgsql backend or are those hardcoded?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Yes you can override the :ref:`default queries <generic-sql-queries>`.
