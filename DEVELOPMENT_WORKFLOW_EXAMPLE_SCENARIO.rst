=====================
The world as it is...
=====================

Branches
========

.. code:
  master (liberty)
  |- 12.0
  |
   -kilo
     |
     |- 11.0
     |- 11.1
      - 11.2

Tags - (latest from each branch)
================================

12.0.1, 11.0.6, 11.1.4, 11.2.2

Major.minor.patch
=================

* Major

  * openstack release
  * possibly feature?
* minor

  * big change within release
  * security  - upgrade-impacting
  * bug fixes - upgrade-impacting
  * features
  * sha bumps
* patch

  * security  - non-upgrade-impacting
  * bug fixes - non-upgrade-impacting

=========
Scenarios
=========

1. Bug gets reported
2. Bug gets milestoned into major.minor.patch (newest revision targeted of the list of versions targeted, github limitation)
3. Bug gets list in top comment for targets of the bugfix

  3.1. Issue is only in liberty and is non-upgrade-impacting
    3.1.1. In this case you would add the following.
      .. code::

        - [ ] Master
        - [ ] 12.0

    3.1.2. The fix would be available upon the tagging of these, assuming it is fixed immediately
      `12.1.0, 12.0.2`

  3.2. Issue is only in liberty and is upgrade-impacting
    3.2.1. In this case you would add the following.
      .. code::

        - [ ] Master

    3.2.2. The fix would be available upon the tagging of these, assuming it is fixed immediately
      `12.1.0`

  3.3. Issue is in all versions and is non-upgrade-impacting
    3.3.1. In this case you would add the following.
      .. code::

        - [ ] Master
        - [ ] 12.0
        - [ ] Kilo
        - [ ] 11.0
        - [ ] 11.1
        - [ ] 11.2

    3.3.2. The fix would be available upon the tagging of these, assuming it is fixed immediately
      `12.1.0, 12.0.2, 11.0.7, 11.1.5, 11.0.3`

  3.4. Issue is in all versions and is upgrade-impacting
    3.4.1. In this case you would add the following.
      .. code::

        - [ ] Master
        - [ ] Kilo

    3.4.2. The fix would be available upon the tagging of these, assuming it is fixed immediately
      `13.0.0, 12.1.0, 11.3.0`
