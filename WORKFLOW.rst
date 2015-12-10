====================================
 RPC OpenStack Development Workflow
====================================

Summary
=======

It is currently necessary, to change the way the RPC OpenStack team develops
the project (i.e., adds features, fixes bugs). The old workflow is described
in the :ref:`Old Workflow <old-workflow>` section.

The new workflow is outlined under :ref:`Proposed Workflow
<proposed-workflow>`

.. _old-workflow:

RPC OpenStack's Old Workflow
============================

Facts
-----

#. |rpcos| is configured with the following branches:

   - ``master``

   - ``kilo``

   When we decide to support liberty, a third branch will appear - ``liberty``.

#. Bug fixes move from ``master`` to the other branch(es) via cherry-picks.

#. Bugs are hand-managed by developers and backports are tracked haphazardly.

#. Developers cannot use GitHub's commit message syntax to auto-close bugs.

#. ``master`` 's current focus is not well defined. (Is it the next minor
   release of the most recent stable branch? Is it the next major release of
   RPC OpenStack?)

Development Workflow
--------------------

#. A bug is filed or a feature is proposed to |rpcos|

#. After triage and prioritization, a developer creates a fix.

#. The fix is pushed to a branch on that developer's fork.

   This prevents another developer from being able to collaborate on that
   change.

#. The developer submits a pull request against ``master``. It's reviewed,
   iterated upon, and eventually merged.

#. If that PR is elligible to be backported, then the associated bug is
   labeled as such and PRs are submitted to the stable branches.

#. Stable PRs are then reviewed and merged.

#. A developer will (eventually) close the original issue.

Release Workflow
----------------

.. note::
    This section is a bit unclear to me even now. It will need refinement.

#. When targetted features, bugs, etc. are merged to the appropriate branch, a
   Release Candidate (RC) tag is created, e.g., ``r11.0.0rc1``.

#. Our QE and Support teams use that tag to test certain scenarios.

#. We receive feedback and bug reports which are fixed. We then go back to
   Step 1 until we have a version with which everyone is the least unhappy.
   The most recent RC tag is also then the final tag, e.g., ``r11.0.0``.

Our official release document (hidden away in a private wiki) states we should
create a branch for a release before it is created, but recently when we
released ``r11.1.0``, we created a separate branch ``kilo-11.1`` after we
decided on the final acceptable ``r11.1.0`` RC tag.

.. _proposed-workflow:

RPC OpenStack's Proposed New Workflow
=====================================

Pre-Workflow
------------

#. Rename ``master`` to be |lsbnext|.

#. Make |lsbnext| the "default" branch on GitHub.

   This assures that all PRs will be targetted to |lsbnext| by default.

.. note::

    When a new stable release of OpenStack is created, |lsbnext| would then be
    renamed to the codename of that release, e.g., ``liberty-next``.

Development Workflow
--------------------

#. After a bug or feature is prioritized and assigned, the developer pushes a
   fix to a branch on |rpcos|. [#]_

#. A PR is sent to target the |lsbnext| branch (if the bug applies to
   |lsbnext|, otherwise it is sent to the most recent branch it affects.

#. All features and bugs are merged to |lsbnext| as appropriate.

#. Bugs meeting backport criteria [#]_ are backported to the previous branches
   as appropriate.

#. Backport PRs are added to the original issue by the developers who create
   them for easier tracking.

   .. code::

       {{ Issue Description }}

       ----

       - [ ] Original PR: https://github.com/rcbops/rpc-openstack/pull/{{number}}

       - [ ] Backport to kilo: https://github.com/rcbpos/...

       - [ ] ...

   Using markdown checklists in GitHub will give others a quick view into the
   status of each pull request.

#. Each time a PR is merged, the associated branch is deleted from |rpcos|.

#. When all PRs are completed the issue is then closed.

Release Workflow
----------------

.. note::

    This proposed release workflow is meant to mimic some of OpenStack's
    practices.

#. Each time a large feature targetted for the next minor release is merged, a
   Beta tag is created, e.g., ``r11.1.0b1``, ``r11.1.0b2``, etc.

#. As we approach the end of features and bugs targetted for a minor release,
   we create a Release Candidate tag, e.g., ``r11.1.0rc1``.

#. Each tag that is created should be tested in depth by QE and/or Support for
   acceptance and confirmation that the acceptance criteria were met. **This
   is designed to create shorter feedback loops.**

#. As bugs are identified in these tags, work is merged to the target branch.

#. When a release is created, branches can be created from |lsbnext| if
   desired by the stakeholders.

Addendum
^^^^^^^^

If branches are desired for every major and minor release (but not patch),
the branch naming scheme should change. An example would be to have
branches named

- ``r10.0``
- ``r10.1``
- ``r11.0``
- ``r11.1``

Bugs can then be backported to the ``r11.0`` and ``r11.1`` series as
necessary.

Alternative New Workflow
========================

The alternate workflow is similar to :ref:`the proposed workflow
<proposed-workflow>`_ except that instead of backporting patches, patches are
applied to the oldest applicable branch first, then second oldest, etc., until
they are finally applied to |lsbnext|. This allows developers to use ``Closes
#bugnumber`` in the commit message and for GitHub to automatically close the
issue for us as a team.

This is the workflow used by the `fabric`_ project.

----

.. Replacements

.. |rpcos| replace:: ``rpc-openstack``
.. |lsbnext| replace:: ``{{latest-stable-branch-name}}-next``

.. Footnotes

.. [#]
    It is suggested that developers use succinct branch names, e.g.,
    ``bug/103`` or ``feature/210``, which are descriptive but also point at
    the item being worked on. Backport PRs might consider branch names like
    ``bug/{{backport-branch}}/103`` (e.g., ``bug/kilo/103``).

.. [#]
    This document suggests using
    http://docs.openstack.org/project-team-guide/stable-branches.html as a
    basis for these backport criteria.

.. Links

.. _fabric: https://github.com/fabric/fabric

.. Metadata

:author: Ian Cordasco
:Created At: 2015-12-10
:Last Modified: 2015-12-10
