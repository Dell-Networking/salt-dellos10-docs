############
Installation
############

This information contains instructions to install SaltStack (Salt) with the `napalm_dellos10 <https://github.com/napalm-automation-community/napalm-dellos10>`_ proxy. Included are instructions for the setup environment for managing Dell EMC Networking OS10 switches

Install Salt
************

Installation of Salt contains three components:

* salt-master
* salt-minion
* salt-proxy

The simplest way to install Salt is via `salt bootstrap <https://docs.saltstack.com/en/latest/topics/tutorials/salt_bootstrap.html>`_.

::

   wget -O bootstrap-salt.sh https://bootstrap.saltstack.com/develop
   sudo sh bootstrap-salt.sh -M

See the `platform-specific instructions <https://docs.saltstack.com/en/latest/topics/installation/#platform-specific-installation-instructions>`_ from the official Saltstack
documentation for more information. Be aware to install the master distribution from the PPA repo, as the local server will run as Master and control the devices as proxy-minions.

See the `CentOS documentation <https://github.com/napalm-automation/napalm-salt/blob/master/centos_installation.md>`_ for more information.

Install NAPALM
**************

Install the Dell EMC Networking OS10 NAPALM driver:

::

   sudo apt-get install libffi-dev libssl-dev python-dev python-cffi libxslt1-dev python-pip
   pip install --upgrade pip
   sudo pip install --upgrade cffi
   sudo pip install napalm-dellos10

You can also install NAPALM using `napalm-install Saltstack formula <https://github.com/saltstack-formulas/napalm-install-formula>`_. See `napalm-install-formula <https://mirceaulinic.net/2017-07-06-napalm-install-formula/>`_ for a more detailed usage example.

Configure salt-proxy
********************

The salt-proxy configuration is shown, and the default location of the salt-proxy configuration file is ``/etc/salt/proxy``.

::

  master: localhost
  multiprocessing: false # turn off multiprocessing
  mine_enabled: true # not required, but nice to have
  pki_dir: /etc/salt/pki/proxy # not required - this separates the proxy keys into a different directory

Configure salt-minion
*********************

The salt-minion configuration is shown, and the default location of the salt-minion configuration file is ``/etc/salt/minion``.

::

  master: localhost

.. _configure_connection_to_device:

Configure connection with device
********************************

In salt-napalm, all switch-specific information such as switch IP address and credentials are configured in the pillar file.

Step 1
======

The default pillar data file location is ``/srv/pillar``. You must create this directory as it will not be available by default (you can change the location later). See `pillar-roots <https://docs.saltstack.com/en/latest/ref/configuration/master.html#pillar-roots>`_ for complete information.

::

   mkdir -p /srv/pillar

Step 2
======

Create a ``top.sls`` file in that directory, which tells the salt-master which minions receive which pillar.

Create and edit the ``/srv/pillar/top.sls`` file and match the example:

::

  base: # Default value, configurable at /etc/salt/master
    LEAF_1: # ``DEVICE_ID``, used to interact with the device, from Salt CLI
      - leaf_1_pillar # ``DEVICE_SLS_FILENAME``, Name of the file containing the specifications of the device
    LEAF_2:
      - leaf_2_pillar


.. note::
   DEVICE_ID, given in the above configuration shall be used in following places,
     1) Run the salt-proxy ``salt-proxy --proxyid=[DEVICE_ID] -l debug``
     2) Connect to device using Salt CLI ``salt 'LEAF_1' test.ping``

.. _pillar_configuration:

Step 2a
=======

Create a ``DEVICE_SLS_FILENAME`` file (mentioned in Step 2) in ``/srv/pillar/leaf_1_pillar.sls``:

::

    proxy:
      proxytype: napalm
      driver: dellos10
      host: 192.168.128.128
      username: my_username
      passwd: my_password
      optional_args:
        global_delay_factor: 3 # This is optional value, increase value in case device response is slow

The ``passwd`` is in plain-text and is used for encrypting the password (see `salt-renderers.gpg <https://docs.saltstack.com/en/latest/ref/renderers/all/salt.renderers.gpg.html>`_).

Start Salt Services
*******************

::

  sudo systemctl start salt-master
  sudo systemctl restart salt-minion

Start proxy-minion for device
=============================

Test the proxy-minion:

::

  sudo salt-proxy --proxyid=[DEVICE_ID] -l debug

On the first connection attempt, the minion cannot talk and is stuck with an error message:

::

  [ERROR   ] The Salt Master has cached the public key for this node, this salt minion will wait for 10 seconds before attempting to re-authenticate
  [INFO    ] Waiting 10 seconds before retry.

This is normal and is due to the salt key from the minion not being accepted by the master. Quit the minion with ``CTRL + C`` and run sudo ``salt-key``.

::

    [root@master ~]# salt-key -L
    Unaccepted Keys:
    LEAF_1
    LEAF_2
    Accepted Keys:

This example shows that the salt-master is aware of four salt-minions, but none of the keys has been accepted. To accept the keys and allow the Minions to be controlled by the salt-master, use the ``salt-key`` command:

::

    [root@master ~]# salt-key -A
    [root@master ~]# salt-key -L
    Unaccepted Keys:
    Accepted Keys:
    LEAF_1
    LEAF_2

The salt-key command allows for signing keys individually or in bulk. The example shows using ``-A`` bulk-accepts all pending keys. To accept keys individually, use the lowercase of the same option (``-a``).

Start the proxy again.

Test your configuration
=======================

Once the key has been accepted, restart the proxy in debug mode and start a separate terminal session:

::

  sudo salt 'LEAF_1' test.ping

To test for all leaf devices:

::

  sudo salt 'LEAF_*' test.ping

It should return True if there are no problems. If everything checks out, hit ``CTRL + C`` and restart ``salt-proxy`` as a daemon.

::

  sudo salt-proxy --proxyid=[DEVICE_ID] -d

Example:

::

  sudo salt-proxy --proxyid=LEAF_1 -d
  sudo salt-proxy --proxyid=LEAF_2 -d

Finally, sync your packages:

::

  sudo salt '*' saltutil.sync_all
