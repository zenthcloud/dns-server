KSK Rollover using CDS & CDNSKEY Key Rollover
=============================================

If the upstream registry supports :rfc:`7344` key rollovers you can use
several :doc:`pdnsutil <../manpages/pdnsutil.1>` commands to do this
rollover. This HowTo follows the rollover example from the RFCs
:rfc:`Appendix B <7344#appendix-B>`.

We assume the zone name is example.com and is already DNSSEC signed.

Start by adding a new KSK to the zone::

  pdnsutil zone add-key example.com ksk 2048 inactive

or, prior to version 5.0::

  pdnsutil add-zone-key example.com ksk 2048 inactive

The "inactive"
means that the key is not used to sign any ZSK records. This limits the
size of ``ANY`` and DNSKEY responses.

Publish the CDS records::

  pdnsutil zone set-publish-cds example.com

or, prior to version 5.0::

  pdnsutil set-publish-cds example.com

These records will tell the parent zone to update its DS records. Now wait for
the DS records to be updated in the parent zone.

Once the DS records are updated, do the actual key-rollover:

.. code-block:: shell

  pdnsutil zone activate-key example.com new-key-id
  pdnsutil zone deactivate-key example.com old-key-id

or, prior to version 5.0:

.. code-block:: shell

  pdnsutil activate-zone-key example.com new-key-id
  pdnsutil deactivate-zone-key example.com old-key-id

You can get the
``new-key-id`` and ``old-key-id`` by listing them through
``pdnsutil zone show example.com`` (``pdnsutil show-zone example.com`` prior to
version 5.0).

After the rollover, wait *at least* until the TTL on the DNSKEY records
have expired so validating resolvers won't mark the zone as BOGUS. When
the wait is over, delete the old key from the zone::

  pdnsutil zone remove-key example.com old-key-id

or, prior to version 5.0::

  pdnsutil remove-zone-key example.com old-key-id

This updates the CDS records to reflect only the new key.

Wait for the parent to pick up on the CDS change. Once the upstream DS
records show only the DS records for the new KSK, you may disable
sending out the CDS responses::

  pdnsutil zone unset-publish-cds example.com

or, prior to version 5.0::

  pdnsutil unset-publish-cds example.com
