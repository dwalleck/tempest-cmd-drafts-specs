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

  Create a workspace: ``tempest ws init <name>``
  List workspaces: ``tempest ws list``
  Delete a workspace: ``tempest ws delete <name>

The convention of referencing workspaces by name can be used by other commands
as well. For example, ``tempest devstack run <args>`` would be shorthand for
using the configurations in the ``devstack`` workspace to run the desired set
of tests.


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

Primary assignee:
  slowrie
  dwalleck

Milestones
----------

Target Milestone for completion:
  Mitaka-2

Work Items
----------

- TBD

References
==========

- https://etherpad.openstack.org/p/tempest-cli-improvements
