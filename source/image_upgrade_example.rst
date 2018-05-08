###########################################################################
Install or upgrade devices running Dell EMC Networking OS10 using SaltStack
###########################################################################

This example describes how to use SaltStack to install or upgrade the software image on a device running Dell EMC Networking OS10.

Step 1
******

Create a salt-proxy for the OS10 switch you want to upgrade or install an image (see :ref:`Configure the connection with a device <configure_connection_to_device>`).

In a new terminal session, test that your switch is configured correctly:

::

  sudo salt '<DEVICE_ID>' test.ping

Step 2
******

Upload the image that you need to install to an TFTP/FTP/SCP/SFTP/HTTP server.

Step 3
******

Install or upgrade the image on the switch. The example image file path should look like ``image file path``: /root/PKGS_OS10-Enterprise-10.4.0E.R2.30-installer-x86_64.bin.

SCP server details:
``Server IP``: 1.1.1.1
``credentials``: username: my_username, password: my_password
``image file path``: /root/PKGS_OS10-Enterprise-10.4.0E.R2.30-installer-x86_64.bin

The ``image_file_url`` should look like ``image_file_url="scp://my_username:my_password@1.1.1.1/root/PKGS_OS10-Enterprise-10.4.0E.R2.30-installer-x86_64.bin"``.

Upgrade the switch image:

::

   sudo salt '<DEVICE_ID>' napalm.call upgrade_switch_image image_file_url="scp://my_username:my_password@1.1.1.1/root/PKGS_OS10-Enterprise-10.4.0E.R2.30-installer-x86_64.bin"


Install the switch image:

::

   sudo salt '<DEVICE_ID>' napalm.call install_switch_image image_file_url="scp://my_username:my_password@1.1.1.1/root/PKGS_OS10-Enterprise-10.4.0E.R2.30-installer-x86_64.bin"

.. note::

   ``image_file_url`` format for TFTP/FTP/SCP/SFTP/HTTP server 

            - ``ftp``:    Install from remote FTP server (ftp://userid:passwd@hostip/filepath)
            - ``http``:   Install from remote HTTP (http://hostip/filepath)
            - ``image``:  Install from image directory (image://filepath)
            - ``scp``:    Install from remote SCP server (scp://userid:passwd@hostip/filepath)
            - ``sftp``:   Install from remote SFTP server (sftp://userid:passwd@hostip/filepath)
            - ``tftp``:   Install from remote TFTP server (tftp://hostip/filepath)
            - ``usb``:    Install from USB directory (usb://filepath)

Step 4
******

Find the status of switch image install or upgrade:

::

   sudo salt '<DEVICE_ID>' napalm.call get_image_status
