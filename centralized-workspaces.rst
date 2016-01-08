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

The current ``tempest init`` command creates a new Tempest workspace in the
directory the user is currently in. However, once these workspaces are created
there is no way to track and manage these workspaces in a consistent manner.
This becomes more problematic as the number of workspaces increases.


Proposed change
===============

Rather than have ``tempest init`` create workspaces in arbitrary directories,
a more consistent approach would be to choose a single directory to contain
Tempest workspaces. This provides a programmatic means to manage workspaces.

+-----------------------+------------------------------+
|        Action         |            Command           |
+=======================+==============================+
| Create a workspace:   | tempest ws init <name>       |
+-----------------------+------------------------------+
| List workspaces:      | tempest ws list              |
+-----------------------+------------------------------+
| Delete a workspace:   | tempest ws delete <name>     |
+-----------------------+------------------------------+
| Run from a workspace: | tempest ws run <name> <args> |
+-----------------------+------------------------------+

Sample Directory Structure
--------------------------
::

  +-.tempest/
    +-devstack/
    +-local/
    +-staging/


Example Usage
-------------
::

  tempest ws init devstack
  tempest ws init staging
  tempest ws run devstack <additional args>
  tempest ws run staging <additional args>


Alternatives
------------

So that workspaces could still be tracked, another option would be to create
a ``.tempest`` file in the user's home directory to be used as a source of
truth for Tempest workspaces. Any other operations would then use this file
as a reference.  This file might not always be up to date if the user manually
moves or deletes the workspace.


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
- Mitaka-3 for run functionality

Work Items
----------

- Create argparse to handle new ``ws`` command and subcommands
- Modify base init Cliff.command to target specific directory rather than the current directory
- Create a way of detecting/tracking workspace related files for deletion
- If the ``.tempest`` file alternative is chosen, handle parsing and deleting of workspace location references


References
==========

- https://etherpad.openstack.org/p/tempest-cli-improvements
