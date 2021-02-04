========
spacemon
========

Free space monitoring tool

This tool will monitor free space on multiple servers
and print alerts to stdout if detects any anomalies.

Installation
------------

- ``cd /opt``
- ``git clone https://github.com/makhomed/spacemon.git``

Upgrade
-------

- ``cd /opt/spacemon``
- ``git pull``


Configuration
-------------

- ``vim /opt/spacemon/spacemon.conf``
- write to config something like this:

.. code-block:: none

    warning 80% 
    host example.com one-line description of this host

Configuration file allow comments, from symbol ``#`` to end of line.

Configuration file has only two directives:
``host`` and ``warning``.

``host`` directive has syntax: ``host <hostname>[:port] [description]``.
``<hostname>`` part is required, it may be domain name or IP address.
``port`` is optional, by default used port 22. ``description`` also optional.

``warning`` directive has syntax ``warning <number>%``. Default value of ``warning``
directive is ``80%``. It can be redefined to any other value, in bounds from 20% to 90%.
Value of ``wanging`` directive will be used for all below host declarations.
For example:

.. code-block:: none

    warning 75%

    host example1
    host example2

    warning 80%

    host example3
    host example4


Command line arguments
----------------------

.. code-block:: none

    spacemon [-c /path/to/configuration/file.conf]

``spacemon`` has optional command line argument ``-c </path/to/configuration/file.conf>``.
If argument ``-c`` not defined - by default will be used configurafion file ``/opt/spacemon/spacemon.conf``.

Before first run
----------------

Before first run you need to create Telegram bot and configure telegram-send script.
Details see in https://pypi.python.org/pypi/telegram-send documentation.

Secure Shell
------------

For work you need to generate private ssh key on ``spacemon`` server
with command ``ssh-keygen -t rsa`` and copy public key from ``/root/.ssh/id_rsa.pub``
to ``/root/.ssh/authorized_keys`` on monitored servers. Also you need to check connection
with monitored server with command ``ssh example.com`` and answer ``yes`` to ssh question:

.. code-block:: none

    # ssh example.com
    The authenticity of host 'example.com' can't be established.
    ECDSA key fingerprint is SHA256:/cYI0bJzEX+CF3DhGEUQ+ZeGFmMzEJYAt3C15450zKs.
    ECDSA key fingerprint is MD5:44:20:bd:f5:aa:a7:52:ac:c5:19:e5:e0:28:2b:90:49.
    Are you sure you want to continue connecting (yes/no)? yes


Send alerts only to email
-------------------------

.. code-block:: none

    0 * * * * root /opt/spacemon/spacemon


Send alerts to email and telegram
---------------------------------

.. code-block:: none

    0 * * * * root /opt/spacemon/spacemon > /tmp/spacemon.txt ; if [ -s /tmp/spacemon.txt ] ; then /usr/local/bin/telegram-send --pre --stdin < /tmp/spacemon.txt ; cat /tmp/spacemon.txt ; fi ; rm /tmp/spacemon.txt

