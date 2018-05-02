############
Installation
############

This section contains instructions to install Salt with `napalm_dellos10 <https://github.com/napalm-automation-community/napalm-dellos10>`_ proxy.
we shall cover the setup environment for managing Dell Networking OS10 switches

Install instruction given below is for Linux Debian.

Install Salt
-------------
We need to install the following salt components,
  1) salt-master
  2) salt-minion
  3) salt-proxy

The simplest way to install Salt the above is via `salt bootstrap <https://docs.saltstack.com/en/latest/topics/tutorials/salt_bootstrap.html>`_.

::

   wget -O bootstrap-salt.sh https://bootstrap.saltstack.com/develop
   sudo sh bootstrap-salt.sh -M

For more specific installation instructions, see the `platform-specific instructions <https://docs.saltstack.com/en/latest/topics/installation/#platform-specific-installation-instructions>`_ from the official Saltstack
documentation. Be aware to install the master distribution from the PPA repo, as on the local server will run as Master,
controlling the devices as Proxy minions.

`CentOS documentation <https://github.com/napalm-automation/napalm-salt/blob/master/centos_installation.md>`_ can be found.

Install Napalm
---------------

Install Dell Netwokking OS10 NAPALM driver,

::

   sudo apt-get install libffi-dev libssl-dev python-dev python-cffi libxslt1-dev python-pip
   sudo pip install --upgrade cffi
   sudo pip install napalm-dellos10

The easy way: Salt users can install NAPALM through a single command, using the `napalm-install Saltstack formula <https://github.com/saltstack-formulas/napalm-install-formula>`_

A more detailed usage example can be `found here <https://mirceaulinic.net/2017-07-06-napalm-install-formula/>`_.

Configure Salt Proxy
---------------------

salt-proxy configuration is as below,

Default location of the salt-proxy configuration file: ``/etc/salt/proxy``

::

  master: localhost
  multiprocessing: false # turn off multiprocessing
  mine_enabled: true # not required, but nice to have
  pki_dir: /etc/salt/pki/proxy # not required - this separates the proxy keys into a different directory


Configure Salt Minion
----------------------

salt-minion configuration is as below,

Default location of the salt-minion configuration file: ``/etc/salt/minion``

::

  master: localhost

.. _configure_connection_to_device:

Configure the connection with a device
---------------------------------------

In Salt-napalm, All the switch specific information like switch IP address, credentials shall be configured in the
pillar file

Step 1
~~~~~~~

Default pillar data file location is ``/srv/pillar``, This directory will not be available by default. We shall create
it. This location can be changed. for `more information click here <https://docs.saltstack.com/en/latest/ref/configuration/master.html#pillar-roots>`_.

::

   mkdir -p /srv/pillar

Step 2
~~~~~~~

we need to create a top.sls file in that directory, which tells the salt-master which minions receive which pillar.

Create and edit the ``/srv/pillar/top.sls`` file and make it look like this:

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

STEP 2a
~~~~~~~~

Create DEVICE_SLS_FILENAME mentioned in Step 2 above:

File to be created at location ``/srv/pillar/leaf_1_pillar.sls``

::

    proxy:
      proxytype: napalm
      driver: dellos10
      host: 192.168.128.128
      username: my_username
      passwd: my_password
      optional_args:
        global_delay_factor: 3 # This is optional value, increase value in case device response is slow

``passwd`` mentioned above is in plain text, for encrypting the password refer `here <https://docs.saltstack.com/en/latest/ref/renderers/all/salt.renderers.gpg.html>`_

Start the Salt Services
------------------------

::

  sudo systemctl start salt-master
  sudo systemctl restart salt-minion

Start the proxy minion for your device
---------------------------------------

Start with testing proxy minion:

::

  sudo salt-proxy --proxyid=[DEVICE_ID] -l debug

On the first connection attempt you will find the that minion cannot talk and is stuck with the following error message:

::

  [ERROR   ] The Salt Master has cached the public key for this node, this salt minion will wait for 10 seconds before attempting to re-authenticate
  [INFO    ] Waiting 10 seconds before retry.

This is normal and is due to the salt key from the minion not being accepted by the master.
Quit the minion with ``CTRL``+ ``C`` and run sudo ``salt-key``

::

    [root@master ~]# salt-key -L
    Unaccepted Keys:
    LEAF_1
    LEAF_2
    Accepted Keys:

This example shows that the Salt Master is aware of four Minions, but none of the keys has been accepted.
To accept the keys and allow the Minions to be controlled by the Master, again use the ``salt-key`` command:

::

    [root@master ~]# salt-key -A
    [root@master ~]# salt-key -L
    Unaccepted Keys:
    Accepted Keys:
    LEAF_1
    LEAF_2

The salt-key command allows for signing keys individually or in bulk.
The example above, using -A bulk-accepts all pending keys.
To accept keys individually use the lowercase of the same option, -a keyname.

Now start the proxy again

Test your configuration
------------------------

Once the key has been accepted, restart the proxy in debug mode and start a separate terminal session.
In your new terminal, issue the following command:

::

  sudo salt 'LEAF_1' test.ping

To test for all leaf devices, run command as below,

::

  sudo salt 'LEAF_*' test.ping

It should return True if there are no problems. If everything checks out, hit ``CTRL``+``C`` and
restart ``salt-proxy`` as a daemon.

::

  sudo salt-proxy --proxyid=[DEVICE_ID] -d

In our case it is

::

  sudo salt-proxy --proxyid=LEAF_1 -d
  sudo salt-proxy --proxyid=LEAF_2 -d

Finally, sync your packages:

::

  sudo salt '*' saltutil.sync_all
