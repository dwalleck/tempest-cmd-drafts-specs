..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.
 http://creativecommons.org/licenses/by/3.0/legalcode

..

==================================
 Tempest Run Command
==================================


https://blueprints.launchpad.net/tempest/+spec/tempest-run-cmd

Implements a domain-specific ``tempest run`` command to be used as the primary
entry point for running Tempest tests.


Problem Description
===================

Currently, the Tempest test suite can be executed using any testr-compatable
runner. While this approach is flexible, it does not provide a consistent
experience for consumers of Tempest. Because these test runners are in no
way specific to Tempest,any items that are domain specific such as
configuration must be performed out of band using shell scripts or other
methods.

An effort is already underway to create a set of Tempest-specific command
line tooling. As part of that effort, this spec defines the ``tempest run``
command.


Proposed Change
===============

Overview
--------

This command will provide a consistent entry point for executing the Tempest
test suite. By creating a runner specific to Tempest

We want to solve for the specutrim of Tempest use cases, ranging from a test
contributor for an specific project to an engineer managing the OpenStack
Infra gates.

Because this runner will be specific to Tempest, intuitive default behaviors
can be created to lower the barrier of entry for new users.

<start notes>
We want to provide a flexible runner that allows for granular control over
the test discovery and execution process that also provides reasonable
default behaviors for less experienced users.

What problems are we trying to solve by creating a Tempest/OpenStack-specific runner?

- Uses smart default behavior
- Highly flexible test selection/discovery
- Parallelism
- Test output
- Built directly on testrepository to leverage future testrepository
  development

We also wanted to address the range of usage scenarios that arise when
running Tempest tests:

- Explicit Class/method name
- Explicit test list in file
- Regex

  - Types
  
    - White list
    - Black list
    
  - Source
  
    - Command line
    - Text file 
</stop notes>


Logical Flow
------------

- Parse command line arguments
- Set necessary environment variables for Tempest based on inputs
- Determine the set of tests to run based on provided filters
- Call into `run_argv`_ or another testrepository entry point with testr
  specific arguments and the list of tests to be executed
- Recieve results from test execution
- Perform any post-processing on results if applicable

.. _run_argv: https://github.com/testing-cabal/testrepository/blob/master/testrepository/commands/__init__.py#L165


Implementation
--------------

Implementing this command has two aspects: the command line interface that
users will interact with and a client that will drive the execution of tests
by interfacing with the testr internals in testrepository.


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
  
  # Included as a discussion point
  # Can we reduce the complexity of regexes by
  # having more granular filtering?
  --package <package filter>
  --module <module filter>
  --method <method filter>
  
  --tag <tag_name>
  - Aliases for common Infra job regexes
    --gate
    --smoke

Output::

  --subunit
  --html <file_name>

Tempest Configuration::

  --config <config_file>
  --accounts <accounts_file>
  --resources <resources_file>

Regex File Format
-----------------

One regex per line in the form of:

the_regex # Comments about the regex


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

- Create ``tempest run`` entry point in Tempest with cliff
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