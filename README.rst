certbot-dns-rcode0
==================

RcodeZero_ DNS Authenticator plugin for certbot_.

This plugin automates the process of completing a ``dns-01`` challenge by
creating, and subsequently removing, TXT records using the `RcodeZero DNS API`_
via lexicon_.

.. _certbot: https://certbot.eff.org/
.. _RcodeZero: https://certbot.eff.org/
.. _RcodeZero DNS API: https://my.rcodezero.at/api-doc
.. _lexicon: https://github.com/AnalogJ/lexicon


Installation
------------

::

    pip install certbot-dns-rcode0


Named Arguments
---------------

To start using DNS authentication for RcodeZero DNS, pass the following arguments on
certbot's command line:

======================================================= =======================
``--authenticator dns-rcode0``                          select the authenticator
                                                        plugin (Required)

``--dns-rcode0-credentials``                            RcodeZero DNS credentials_
                                                        INI file. (Required)

``--dns-rcode0-propagation-seconds``                    | waiting time for DNS to propagate before asking
                                                        | the ACME server to verify the DNS record.
                                                        | (Default: 10, Recommended: >= 600)
======================================================= =======================

You may need to set an even higher propagation time (>= 900 seconds) to give
the RcodeZero DNS network time to propagate the entries! This may be annoying when
calling certbot manually but should not be a problem in automated setups.

Credentials
-----------

Use of this plugin requires a configuration file containing RcodeZero DNS API
ACME token, obtained from your RcodeZero `account page`_. See also the 
`RcodeZero DNS API`_ documentation.

.. _account page: https://my.rcodezero.at/enableapi
.. _RcodeZero DNS API: https://my.rcodezero.at/api-doc

An example ``credentials.ini`` file:

.. code-block:: ini

   dns_rcode0_api_key  = acme_0123456789abcdef0123456789abcdef01234567

The path to this file can be provided interactively or using the
``--dns-rcode0-credentials`` command-line argument. Certbot  records the path to this file 
for use during renewal, but does not store the file's contents.

**CAUTION:** You should protect the API token as you would protect the
password to your RcodeZero account. Users who can read this file can use these
credentials to issue arbitrary API calls on your behalf. Users who can cause
Certbot to run using these credentials can complete a ``dns-01`` challenge to
acquire new certificates or revoke existing certificates for any domain under the 
RcodeZero account, even if those domains aren't being managed by this server. 

Certbot will emit a warning if it detects that the credentials file can be
accessed by other users on your system. The warning reads "Unsafe permissions
on credentials configuration file", followed by the path to the credentials
file. This warning will be emitted each time Certbot uses the credentials file,
including for renewal, and cannot be silenced except by addressing the issue
(e.g., by using a command like ``chmod 600`` to restrict access to the file).


Examples
--------

To acquire a single certificate for both ``example.com`` and
``*.example.com``, waiting 900 seconds for DNS propagation:

.. code-block:: bash

   certbot certonly \
     --authenticator dns-rcode0 \
     --dns-rcode0-credentials ~/.secrets/certbot/rcode0.ini \
     --dns-rcode0-propagation-seconds 900 \
     --server https://acme-v02.api.letsencrypt.org/directory \
     -d 'example.com' \
     -d '*.example.com'
