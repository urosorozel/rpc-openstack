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

#. Bugs are hand-managed by developers. Backport pull requests are submitted
   and merged and the original bug is often not updated in a timely manner.

#. Developers cannot use GitHub's commit message syntax to auto-close bugs.

#. ``master`` 's current focus is not well defined. (Is it the next minor
   release of the most recent stable branch? Is it the next major release of
   RPC OpenStack?)

Workflow
--------

#. A bug is filed or a feature is proposed to |rpcos|

#. After triage and prioritization, a developer creates a fix.

#. The fix is pushed to a branch on that developer's fork.

   This prevents another developer from being able to collaborate on that
   change (unless they're given access to that repository).

#. The developer submits a pull request against ``master``. It's reviewed,
   iterated upon, and eventually merged.

#. If that PR is elligible to be backported, then the associated bug is
   labeled as such and PRs are submitted to the stable branches.

#. Stable PRs are then reviewed and merged.

#. A developer will (eventually) close the original issue.

.. _proposed-workflow:

RPC OpenStack's Proposed New Workflow
=====================================

Pre-Workflow
------------

#. Add documentation to ``master`` that is easily discoverable detailing the
   current development focus for ``master``.

Workflow
--------

#. After a bug or feature is prioritized and assigned, the developer pushes a
   fix to a branch on |rpcos|. [#]_

#. A PR is created from the branch on |rpcos|, reviewed, iterated upon and
   eventually merged.

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
   status of each pull request while viewing all issues as well as the
   individual issue.

#. Each time a PR is merged, the associated branch is deleted from |rpcos|.

   The task on the original issue should also be marked as completed.

#. When all PRs are completed the issue is then closed by a developer.

Notes from our developer meeting
--------------------------------

Here are some notes from yesterday. I apologise for the verbosity and lack of
diagrams and hope that the words will do the ideas justice. If not, let me know
and I will try and knock something up (or someone else can if my words make
sense to them).

The discussion centred around the fact that we need to decide a branching, as
well as tagging strategy.  A large part of the focus is being able to develop a
strategy whereby we can work on, test (that is, QE have a fairly static codebase
to test and for us to fix), and release new features and versions without
impacting our ability to maintain a stable codebase for current releases. What we
discussed was the following (using kilo as an example):

* We have a kilo branch where we do bug fixing and releasing of patch versions.
  Current release is `11.1.x`.

* When we want to develop features toward a next minor (say `11.2`), we would create
  a `11.2` feature branch and develop features there, periodically rebasing against
  kilo branch. This leaves the main kilo branch as `11.1.x` and allows us to continue
  bug fixing there, and releasing patch versions there, whilst not getting polluted
  with 11.2 code.

* When we are ready to pass a release candidate of `11.2` to qe, we would just tag the
  rc in that feature branch.  QE can effectively take as long as they need to test
  it. We would only cherry pick bug fixes from the kilo branch into the feature branch
  (now a kind of rc branch) as QE demanded, thereby not ‘moving the goalposts’ while
  they are testing. We would iterate through maybe a few rc tags until QE are happy

* When QE are happy with, say rc3, we would tag the release there, and then merge
  all that code into kilo. The current kilo release would now be `11.2.x`, and there
  would be no more `11.1.x` releases

* When we want to start working on `11.3.x`, rinse and repeat

* **pros**: keeps rc branch clean for QE, only ever one main release branch, and one
  feature branch. cons: requires constant rebasing of feature branch during development,
  and large merge at release time

A slight alternative arose when I was chatting with Harry and Jesse afterwards. I
apologise this discussion was not had in the meeting, but I will lay it out here
for your consideration nonetheless (I gather this may be similar to what Ian had
already laid out):

* we have a `kilo.next` (or `11.next`) branch where we do feature development as well as
  bug fixing. This is where we would be developing the features for whatever the next
  release would be, so using the same example above it would be for `11.2`

* We also have an `11.1` branch which is our *current release*. Pertinent bug fixes
  would be cherry-picked into here (because they would always be fixed in `11.next`
  first as that is the main development branch), and this is where we would tag and
  release our `11.1.x` patch releases

* When we are ready to pass an `11.2` release candidate to QE, we would create the
  `11.2` branch and tag the rc there. The same *cherry pick only the things that QE
  find* approach would be used, and we would iterate through a number of release
  candidate tags in this branch. Once we are ready to release `11.2.0`, we tag it
  here and keep the branch around as this is now where we would do our releases for
  `11.2.x`

* `Kilo.next` (or `11.next`) at this point becomes where `11.3` features would get
  developed (and same as before, where ALL bugs get initially fixed).

* At this point we could/would also get rid of the `11.1` branch as `11.2` is now the
  current release

* **pros**: no need for feature branch merges, keeps rc branch clean for QE. Branch
  names can be fully explicit for clarity (eg. `devel/11.next`, `stable/11.1`,
  `stable/11.2`)

* **cons**: for the rc period prior to release, there would be a double cherry pick
  process for some bugs (from `.next` into both `11.1` and `11.2` branches).

**NOTE**: In this model, there are no feature branches as such. the `x.next` branch
should always pass a comprehensive gate. If clean (i.e. gate passing) collaboration
can’t happen here, then the possibility of using a short lived feature branch would
still be open

----

.. Replacements

.. |rpcos| replace:: ``rcbops/rpc-openstack``
.. |lsbnext| replace:: ``{{latest-stable-branch-codename}}``

.. Footnotes

.. [#]
    It is suggested that developers use succinct branch names, e.g.,
    ``bug/103`` or ``feature/210``, which are descriptive but also point at
    the item being worked on. Backport PRs might consider branch names like
    ``bug/{{backport-branch}}/103`` (e.g., ``bug/kilo/103``).

.. [#]
    This document suggests using
    http://docs.openstack.org/project-team-guide/stable-branches.html as a
    basis for these backport criteria or creating our own *well-documented*
    criteria based on the linked criteria.

.. Metadata

:author: Ian Cordasco
:Created At: 2015-12-10
:Last Modified By: Ian Cordasco
:Last Modified At: 2015-12-17
