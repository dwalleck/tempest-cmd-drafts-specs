..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.
 http://creativecommons.org/licenses/by/3.0/legalcode

..

==================================
 Tempest Run Command
==================================

https://blueprints.launchpad.net/tempest/+spec/tempest-run-cmd

Describes a domain-specific ``tempest run`` command to be used as the primary
entry point for running Tempest tests.


Problem Description
===================

There are a wide range of Tempest use cases ranging from OpenStack gate
testing to the testing of existing public and private clouds across multiple
environments and configurations. Each of these user scenarios has its own
requirements and challenges.

The Tempest test suite currently can be executed using any testr-compatable
runner. While this allows for some flexibility, it does not provide a
consistent experience for consumers of Tempest. In addition, because these
test runners are in no way specific to Tempest any items that are domain
specific such as configuration must be performed out-of-band using shell
scripts or other methods.

Since an effort is already underway to create a set of Tempest-specific
command line tooling, this spec further defines a ``tempest run`` command.
This spec addresses the following problems:

- Providing a flexible runner that enables multiple approaches to the test
  discovery and execution processes
- Facilitating ease of configuration and execution of Tempest across multiple
  environments and configurations
- Extends testrepository directly in order to leverage current and future
  testrepository features


Proposed Change
===============

Command Line Interface
----------------------

The list of proposed command line arguments are as follows:

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
  
  --test-dir <test package>
  --package <package filter>
  --tag <tag name>

  --include <regex or file name>
  --exclude <regex or file name>
    
    File format:
    
    regex1 # Comments about this regex
    another_regex

Aliases for most commonly used regexes::

    --gate
    --smoke

Output::

  --subunit
  --html <file name>

Tempest Configuration::

  --config <config file>
  --accounts <accounts file>


Implementation
--------------

The logical flow of the proposed test runner is as follows:

- Parse any command line arguments
- Set necessary environment variables for Tempest based on inputs
- Determine the set of tests to run based on the provided regexes and
  other filters
- Call into `run_argv`_ or another testrepository entry point with
  testr-specific arguments and the list of tests to be executed
- Recieve results from test execution
- Perform any post-processing on results if applicable

.. _run_argv: https://github.com/testing-cabal/testrepository/blob/master/testrepository/commands/__init__.py#L165


Projects
========

* openstack/tempest

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

- Create a ``tempest run`` entry point in Tempest based on the cliff package
- Handle setup of Tempest specific options such as Tempest configuration
- Implement test selection logic based on the provided filtering
  options (regexes, tags, etc.)
- Create the client code that will interact with testrepository and
  control test execution and results gathering
- Implement handlers for any non-subunit output formats


References
==========

- `Mitaka Design Summit CLI Session`_

.. _Mitaka Design Summit CLI Session: https://etherpad.openstack.org/p/mitaka-qa-tempest-run-cli

Previous Implementations and Specs

- `os-testr runner`_
- `Prototype by mtreinish`_
- `Previous Tempest CLI spec`_


.. _os-testr runner: https://github.com/openstack/os-testr/blob/master/os_testr/os_testr.py
.. _Prototype by mtreinish: https://review.openstack.org/#/c/197378/8/tempest/cmd/run.py
.. _Previous Tempest CLI spec: https://github.com/openstack/qa-specs/blob/master/specs/tempest/tempest-cli-improvements.rst
