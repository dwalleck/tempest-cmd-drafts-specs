..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.
 http://creativecommons.org/licenses/by/3.0/legalcode

..

=========================================
 Centralized Tempest Workspace Management
=========================================

<blueprint>

Create a consistent means for creation and management of Tempest workspaces.


Problem description
===================

Currently there is no way to track workspaces in a consistent manner. This
becomes problematic as the number of workspaces increases.


Proposed change
===============

Create a ``.tempest`` file in the user's home directory to be used as a source
of truth for Tempest workspaces. Users can register new workspaces via the
``tempest ws register`` command. The tracking file will automatically remove any
deleted workspaces when ``tempest ws list`` is ran.

+-----------------------+------------------------------+
|        Action         |            Command           |
+=======================+==============================+
| Register a workspace: | tempest ws register <name>   |
+-----------------------+------------------------------+
| List workspaces:      | tempest ws list              |
+-----------------------+------------------------------+

Example Usage
-------------
::

  > cd ~/devstack
  > tempest init
  > tempest ws register devstack

  > cd /etc/staging
  > tempest init
  > tempest ws register staging

  > tempest ws list
  +----------------+--------------------------+
  | Name           | Location                 |
  +----------------+--------------------------+
  | devstack       | /root/devstack           |
  | staging        | /etc/staging             |
  +----------------+--------------------------+


Alternative to Register
-----------------------

Rather than having a register command, the registration could be moved into
``tempest init``. This would add a new parameter for name into init which would
be optional. If a name is not specified it would create a unique name.


Example of Alternative
----------------------

::

  > cd ~/devstack
  > tempest init devstack

  > cd /etc/staging
  > tempest init

  > tempest ws list
  +--------------------------------------+----------------+
  | Name                                 | Location       |
  +--------------------------------------+--------------- +
  | devstack                             | /root/devstack |
  | dae9b977-dc04-4d36-8d38-e28fc624384c | /etc/staging   |
  +--------------------------------------+----------------+


Projects
========

* openstack/tempest


Implementation
==============

Assignee(s)
-----------

* slowrie
* dwalleck

Milestones
----------

Target Milestone for completion:

- Mitaka-2

Work Items
----------

- Create argparse to handle new ``ws`` command and subcommands
- Create tracking file and class to represent it
- Add code to list that deletes workspaces when locations no longer exist


References
==========

- https://etherpad.openstack.org/p/tempest-cli-improvements
