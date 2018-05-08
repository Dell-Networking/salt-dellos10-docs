##########################################
Dell EMC Networking OS10 SaltStack modules
##########################################

Network module
**************

The `network module <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html>`_ facilitates the basic methods for interaction with the network device through the virtual
proxy 'napalm'.

*  `cli <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html#salt.modules.napalm_network.cli>`_: returns a dictionary with the raw output of all commands passed as arguments
*  `commit <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html#salt.modules.napalm_network.commit>`_: commits the NAPALM's candidate configuration changes made on the network device
*  `compare_config <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html#salt.modules.napalm_network.compare_config>`_: returns the difference between the running configuration and the candidate configuration
*  `config <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html#salt.modules.napalm_network.config>`_: returns the whole configuration of the network device
*  `facts <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html#salt.modules.napalm_network.facts>`_: returns characteristics of the network device
*  `interfaces <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html#salt.modules.napalm_network.interfaces>`_: returns details of the interfaces on the device
*  `lldp <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html#salt.modules.napalm_network.lldp>`_: returns a detailed view of the LLDP neighbors
*  `load_config <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html#salt.modules.napalm_network.load_config>`_: applies configuration changes on the device; it can be loaded from a file or from inline string; if you send both a filename and a string containing the configuration, the file has higher precedence
*  `load_template <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html#salt.modules.napalm_network.load_template>`_: renders a configuration template (default: Jinja) and loads the result on the device
*  `ping <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_network.html#salt.modules.napalm_network.ping>`_: executes a ping on the network device and returns a dictionary as a result

BGP module
**********

The `bgp module <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_bgp.html>`_ manages BGP configuration on network devices and provides statistics.

*  `config <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_bgp.html#salt.modules.napalm_bgp.config>`_: returns the BGP configuration on the device
*  `neighbors <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_bgp.html#salt.modules.napalm_bgp.neighbors>`_: returns details regarding the BGP sessions configured on the network device

Route module
************

The `route module <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_route.html>`_ retrieves route details from network devices.

*  `show <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_route.html#salt.modules.napalm_route.show>`_: returns all details for a certain route learned via a specific protocol; if the protocol is not specified, all possible routes display

SNMP module
***********

The `SNMP module <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_snmp.html>`_ Manages SNMP on network devices.

*  `config <https://docs.saltstack.com/en/develop/ref/modules/all/salt.modules.napalm_snmp.html#salt.modules.napalm_snmp.config>`_: returns the SNMP configuration
