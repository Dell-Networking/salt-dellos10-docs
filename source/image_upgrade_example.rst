============================================================================
Use Saltstack to install or upgrade devices running Dell EMC Networking OS10
============================================================================

This example explains how to use Saltstack to install or upgrade the software image on a device running Dell EMC Networking OS10.


STEP 1
~~~~~~

Create a salt-proxy for the OS10 switch you want to upgrade or install image. see :ref:`Configure the connection with a device <configure_connection_to_device>`

In new terminal, issue the following command and to test your switch is configured correctly

::

  sudo salt '<DEVICE_ID>' test.ping


STEP 2
~~~~~~~

Upload the image you need to install to any of the TFTP/FTP/SCP/SFTP/HTTP server

STEP 3
~~~~~~~
Install or upgrade the image on the switch by running the command below,

Example image file path:

SCP server details are below,
``Server IP``: 1.1.1.1
``credentials``: username: my_username, password: my_password
``image file path``: /root/PKGS_OS10-Enterprise-10.4.0E.R2.30-installer-x86_64.bin

then our ``image_file_url`` should look as below

``image_file_url="scp://my_username:my_password@1.1.1.1/root/PKGS_OS10-Enterprise-10.4.0E.R2.30-installer-x86_64.bin"``

To upgarde the switch image run the below command:

::

   sudo salt '<DEVICE_ID>' napalm.call upgrade_switch_image image_file_url="scp://my_username:my_password@1.1.1.1/root/PKGS_OS10-Enterprise-10.4.0E.R2.30-installer-x86_64.bin"


To install the switch image run the below command:

::

   sudo salt '<DEVICE_ID>' napalm.call install_switch_image image_file_url="scp://my_username:my_password@1.1.1.1/root/PKGS_OS10-Enterprise-10.4.0E.R2.30-installer-x86_64.bin"

.. note::

   ``image_file_url`` format for TFTP/FTP/SCP/SFTP/HTTP server are below

            - ``ftp``:    Install from remote FTP server (ftp://userid:passwd@hostip/filepath)
            - ``http``:   Install from remote HTTP (http://hostip/filepath)
            - ``image``:  Install from image directory (image://filepath)
            - ``scp``:    Install from remote SCP server (scp://userid:passwd@hostip/filepath)
            - ``sftp``:   Install from remote SFTP server (sftp://userid:passwd@hostip/filepath)
            - ``tftp``:   Install from remote TFTP server (tftp://hostip/filepath)
            - ``usb``:    Install from USB directory (usb://filepath)

STEP 4
~~~~~~

To find the status of switch image install or upgarde, run the below command:

::

   sudo salt '<DEVICE_ID>' napalm.call get_image_status
