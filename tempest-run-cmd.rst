..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.
 http://creativecommons.org/licenses/by/3.0/legalcode

..

==================================
 Tempest Run Command
==================================


<blueprint>

Implements a domain-specific ``tempest run`` command to be used as the primary
entry point for running Tempest tests.


Problem Description
===================

We want to provide a flexible runner that allows for granular control over
the test discovery and execution process that also provides reasonable
default behaviors for less experienced users.

What problems are we trying to solve by creating a Tempest/OpenStack-specific runner?

- Uses smart default behavior
- Test selection/discovery
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

Proposed Change
===============

Implementing this command has two aspects: the command line interface that
users will interact with and a client that will drive the execution of tests
by interfacing with the testr internals in testrepository.

Steps

- parse command line args
- save any command line args that should be directly passed to testr
- determine the set of tests to run based on command line args
- call into run_argv with the testr specific commands and list of tests determined in the previous step
- receive subunit results from testr
- Perform any post-processing on results if applicable
- print results to console
- Write out files if necessary

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