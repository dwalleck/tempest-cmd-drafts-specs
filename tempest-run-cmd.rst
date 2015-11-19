..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.
 http://creativecommons.org/licenses/by/3.0/legalcode

..

==================================
 Tempest Run Command
==================================


<blueprint>

Implements a flexible ``tempest run`` command to be used as the primary
entry point for running Tempest tests.


Problem description
===================

We want to provide a flexible runner that allows for granular control over
the test discovery and execution process that also provides reasonable
default behaviors for less experienced users.

What problems are we trying to solve by creating a Tempest/OpenStack-specific runner?

- Uses smart default behavior
- Test selection/discovery
- Parallelizsm
- Test output

Usage Scenarios
- Regex
  - Types
    - White list
    - Black list
  - Source
    - Command line
    - Text file 



Proposed change
===============

Implementing this spec has two primary aspects: the internals of the runner
that will interact directly with the testrepository.ui API, and the command
line interface that will be exposed to users.

Internals
---------

Command Line Interface
----------------------

tempest run

Test Execution::

  --fail-fast
  --exec-mode <serial/parallel>  
  --workers <workers>
  --list
  --list-pretty
    
    Example:
    
    +-tempest/
      +-api/
        +-compute/
        | test_authorization.py
        | test_quotas.py
        +-image/
          +-v1/
          | test_image_members.py
          | test_images.py
          | test_images_negative.py
          +-v2/
          | test_image_member.py
          | test_images.py
          | test_images_negative.py

Test Selection/Discovery::

  --include <regex or file name>
  --exclude <regex or file name>
  --tag <tag_name>
  - Aliases for common Infra job regexes
    --gate
    --smoke

Output::

  --subunit

Optional
--------

  --config <config_file>
  --accounts <accounts_file>
  --resources <resources_file>

Projects
========

* openstack/tempest

Implementation
==============

Assignee(s)
-----------

Primary assignee:
  dwalleck
  sammyd
  slowrie

Milestones
----------

Target Milestone for completion:
  Mitaka-3

Work Items
----------

- <items>

Dependencies
============

- Mitaka-3

References
==========

- https://etherpad.openstack.org/p/mitaka-qa-tempest-run-cli
- https://github.com/testing-cabal/testrepository/tree/master/testrepository/ui

Previous implementations/specs

- https://github.com/openstack/os-testr/blob/master/os_testr/os_testr.py
- https://review.openstack.org/#/c/197378/8/tempest/cmd/run.py
- https://github.com/openstack/qa-specs/blob/master/specs/tempest/tempest-cli-improvements.rst
