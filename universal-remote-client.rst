..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.
 http://creativecommons.org/licenses/by/3.0/legalcode

..

==================================
 Universal Remote Client
==================================


<blueprint>

Creates a universal remote client that can be implemented for any number of
operating systems.


Problem description
===================

The current implementation of the Tempest remote client uses command line
tools that are common to many Linux distributions to gather data on the
guest operating system. However, any number of other operating systems
are supported by OpenStack Compute such as BSD, Solaris, and Windows.
To allow Tempest to be run regardless of the operating system of the
configured image, the remote client should provide a generic interface that
can be implemented for any operating system. 


Proposed change
===============

- Create an abstract base class that defines the methods that must be
implemented in order for Tempest to gather the data necessary for its tests
- Modify the existing Linux remote client to implement the abstract base
- Modify any tests as necessary to use the abstract base
- Create additional implementations for Windows and Solaris as a proof
of concept 

Projects
========

* openstack/tempest

Implementation
==============

Assignee(s)
-----------

Primary assignee:
  dwalleck

Milestones
----------

Target Milestone for completion:
  Mitaka-2

Work Items
----------

- TBD

Dependencies
============

- TBD

References
==========

- Abstract Remote Client (https://github.com/openstack/cloudcafe/blob/master/cloudcafe/compute/common/clients/remote_instance/base_client.py)
- Windows Remote Client based on pywinrm (https://github.com/openstack/opencafe/blob/master/cafe/plugins/winrm/cafe/engine/winrm/client.py)