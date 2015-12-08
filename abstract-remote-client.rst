..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.
 http://creativecommons.org/licenses/by/3.0/legalcode

..

==================================
 Abstract Remote Client
==================================


https://blueprints.launchpad.net/tempest/+spec/abstract-remote-client

Creates an abstract model for remote clients which allows for the
development of remote clients beyond the scope of the generic Linux case.
It also provides the ability to create remote clients that use protocols
other than SSH.


Problem Description
===================

The current implementation of the Tempest remote client uses command line
tools that are common to many Linux distributions to gather data from the
guest operating system. While this solution solves testing the general
case, there are a number of guest operating systems such as Solaris, FreeBSD,
and other less mainstream Linux distros that the existing remote client will
not work with.

The current remote client also assumes that SSH as the protocol
for connecting to remote guest and executing commands. This complicates
testing guest operating systems that are not running SSH such as Windows.

To allow Tempest tests to run regardless of the operating system of the
configured image, the remote client should provide a generic interface
that can be implemented for any guest operating system. 


Proposed Change
===============

The proposed solution to this design issue is to provide extendable base
classes for both the remote client and its driver. By doing so, testers
and operators can provide implementations as necessary to allow for
the images they use for testing. The tester would list the operating system
of their image as part of the data in the resources.yaml, which would be used
by the tests to load the correct remote client implementation. The following
is an outline of the proposed solution.

Proposed Class Diagram:

.. image:: https://www.lucidchart.com/publicSegments/view/002a4f91-dc2f-4603-aa44-04740fe73d43/image.png

Driver Pseudocode::

    class BaseDriver(object):
    
        __metaclass__ = abc.ABCMeta
    
        @abc.abstractmethod
        def exec_command(self, command):
            return
    
    class SSHDriver(BaseDriver):
    
        def __init__(self):
            self.client = SSHClient()
    
        def exec_command(self, command):
            return self.client.exec_command(command)
    
    
    class WinRMDriver(BaseDriver):
    
        def __init__(self):
            self.client = Protocol()
    
        def exec_command(self, command):
            return self.client.run_command(command)


Remote Client Pseudocode::

    class BaseRemoteClient(object):
    
        __metaclass__ = abc.ABCMeta
    
        def __init__(self, driver):
            self.driver = driver
    
        @abc.abstractproperty
        def get_number_of_cpus_cmd(self):
            return
    
        @abc.abstractproperty
        def get_disk_size_cmd(self, disk_name=None):
            return
    
        @abc.abstractproperty
        def get_hostname_cmd(self):
            return
    
        @abc.abstractmethod
        def _parse_get_number_of_cpus_cmd(self, output):
            return
    
        @abc.abstractmethod
        def _parse_get_disk_size_cmd(self, output):
            return
    
        @abc.abstractmethod
        def _parse_get_hostname_cmd(self, output):
            return
    
        def get_number_of_cpus(self):
            output = self.driver.execute(self.get_number_of_cpus_cmd)
            return self._parse_get_number_of_cpus_cmd(output)
    
        def get_disk_size(self, disk_name):
            output = self.driver.execute(self.get_disk_size_cmd)
            return self._parse_get_disk_size_cmd(output)
    
        def get_hostname(self):
            output = self.driver.execute(self.get_hostname_cmd)
            return self._parse_get_hostname_cmd(output)
    
        @staticmethod
        def get_remote_client(self, client_type):
            return(eval(client_type))
    
    
    class LinuxRemoteClient(BaseRemoteClient):
    
        @property
        def get_number_of_cpus_cmd(self):
            return 'cat /proc/cpuinfo | grep processor | wc -l'
    
        @property
        def get_disk_size_cmd(self, disk_name=None):
            return
    
        @property
        def get_hostname_cmd(self):
            return 'hostname'
    
        def _parse_get_hostname_cmd(self, output):
            return output
    
        def _parse_get_number_of_cpus_cmd(self, output):
            return int(output)
    
        def _parse_get_disk_size_cmd(self, output):
            # Implementation
            return
    
    
    class FreeBSDRemoteClient(LinuxRemoteClient):
        
        @property
        def get_number_of_cpus_cmd(self):
            return 'sysctl hw.ncpu'
        
        @property
        def get_disk_size_cmd(self, disk_name='ada0'):
            return 'gpart show -p | grep "{disk_name} "'.format(
                disk_name=disk_name)
    
        def _parse_get_number_of_cpus_cmd(self, output):
            cpus = output.split(':')[1].strip()
            return int(cpus)
    
        def _parse_get_disk_size_cmd(self, output):
            output = output.replace('\n', '')
            disk_size = re.search(r'([0-9]+)G', output).group(1)
            return int(disk_size)
    
    
    class WindowsRemoteClient(BaseRemoteClient):
    
        @property
        def get_number_of_cpus_cmd(self):
            return ('powershell gwmi Win32_ComputerSystem-Property '
                    'NumberOfLogicalProcessors')
    
        @property
        def get_disk_size_cmd(self, disk_name='0'):
            return 'powershell "&{ Get-Disk | Format-List }"'
    
        @property
        def get_hostname_cmd(self):
            return 'hostname'
    
        def _parse_get_hostname_cmd(self, output):
            return output
    
        def _parse_get_number_of_cpus_cmd(self, output):
            return int(output)
    
        def _parse_get_disk_size_cmd(self, output):
            # Implementation
            return

Drawbacks
---------

While a majority of the implementation is a rearrangement of existing
code, it does add complexity to the remote client class structure.
This design also requires that any driver or remote client implementation
must live in tree with Tempest. This means that all implementations should
be tested in some way to ensure that they function after a given change.
To reduce the maintenance burden, I would suggest making both the driver
and remote client extendable via plugins, which would allow external teams
to create and maintain their own implementations without burdening the core
Tempest testing process. If creating a plugin architecture is deemed critical,
that step could be rolled into this spec as well.

Projects
========

* openstack/tempest

Implementation
==============

- Define the base driver class with methods for connecting to and executing
  commands given a protocol (SSH, WinRM, etc)
- Create the base class remote client class that defines the
  methods that must be implemented to gather basic information about the
  targeted guest operating system
- Modify the existing SSH client to implement the base driver interface
- Create a WinRM implementation of the base driver class
- Modify the existing Linux remote client to implement the remote client base
  class
- Modify tests as necessary to use the remote client interface
- Create additional remote client implementations for Windows and Solaris

Assignee(s)
-----------

Primary assignee:
  dwalleck
  sammyd

Milestones
----------

Target Milestone for completion:
  Mitaka-3

Dependencies
============
- Tempest resources spec (https://review.openstack.org/#/c/173334/7)

References
==========

- Proposed abstract driver and remote client (https://gist.github.com/dwalleck/ac95508280b1769686a8)
- Existing out of tree abstract base remote client (https://github.com/openstack/cloudcafe/blob/master/cloudcafe/compute/common/clients/remote_instance/base_client.py)
- Windows Remote Client based on pywinrm (https://github.com/openstack/opencafe/blob/master/cafe/plugins/winrm/cafe/engine/winrm/client.py)

                    +------------+
                    | BaseDriver |
                    +------------+
                         ^  ^
                         |  |
                         |  |
+-------------+          |  |          +------------+
|             +<---------+  +--------->+            |
+-------------+                       +------------+
