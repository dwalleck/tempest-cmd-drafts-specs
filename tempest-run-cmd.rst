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
entry point for running tests.


Problem description
===================



Proposed change
===============

tempest run

  --config <config_file>
  --accounts <accounts_file>
  --resources <resources_file>
  --pattern-include <regex>
  --pattern-exclude <regex>
  --tag <tag1>
  --dry-run (processes any other arguments and then outputs the list 
             of tests that would be executed)
  --from-file <filename>
    - file list
    - regexes?


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
  <release>

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
