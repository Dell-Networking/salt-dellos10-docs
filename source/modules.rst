##########################################
Dell EMC Networking OS10 Saltstack modules
##########################################

Network module
--------------
The `network module <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html>`_ facilitates the basic methods for interaction with the network device through the virtual
proxy 'napalm'.

-  `cli <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html#salt.modules.napalm_network.cli>`_: Returns a dictionary with the raw output of all commands passed as arguments.
-  `commit <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html#salt.modules.napalm_network.commit>`_ : Commits the napalm's candidate configuration changes made on the network device.
-  `compare_config <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html#salt.modules.napalm_network.compare_config>`_: Returns the difference between the running config and the candidate config.
-  `config <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html#salt.modules.napalm_network.config>`_: Return the whole configuration of the network device.
-  `facts <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html#salt.modules.napalm_network.facts>`_: Returns characteristics of the network device.
-  `interfaces <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html#salt.modules.napalm_network.interfaces>`_: Returns details of the interfaces on the device.
-  `lldp <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html#salt.modules.napalm_network.lldp>`_: Returns a detailed view of the LLDP neighbors.
-  `load_config <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html#salt.modules.napalm_network.load_config>`_: Applies configuration changes on the device. It can be loaded from a file or from inline string. If you send both a filename and a string containing the configuration, the file has higher precedence.
-  `load_template <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html#salt.modules.napalm_network.load_template>`_: Renders a configuration template (default: Jinja) and loads the result on the device.
-  `ping <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html#salt.modules.napalm_network.ping>`_: Executes a ping on the network device and returns a dictionary as a result.

BGP module
----------
The `bgp module <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_bgp.html>`_ manages BGP configuration on network devices and provides statistics.

-  `config <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_bgp.html#salt.modules.napalm_bgp.config>`_: Provides the BGP configuration on the device.
-  `neighbors <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_bgp.html#salt.modules.napalm_bgp.neighbors>`_ : Provides details regarding the BGP sessions configured on the network device.

Route module
------------
The `route module <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_route.html>`_ Retrieves route details from network devices.

-  `show <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_route.html#salt.modules.napalm_route.show>`_: Displays all details for a certain route learned via a specific protocol. If the protocol is not specified, will return all possible routes.

SNMP module
-----------
The `SNMP module <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_snmp.html>`_ Manages SNMP on network devices.

-  `config <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_snmp.html#salt.modules.napalm_snmp.config>`_: Returns the SNMP configuration
