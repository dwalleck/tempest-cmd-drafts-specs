..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.
 http://creativecommons.org/licenses/by/3.0/legalcode

..

==================================
 Tempest List Command
==================================


<blueprint>

Provides a means to list all existing tests to run via the Tempest command
line tooling.


Problem description
===================


Proposed change
===============

- List all core tests
- List tests from plugins with special distinction?
- Pretty print

::

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
  Mitaka-3

Work Items
----------

- <items>

Dependencies
============

- <dependencies>

References
==========

- https://etherpad.openstack.org/p/tempest-cli-improvements
