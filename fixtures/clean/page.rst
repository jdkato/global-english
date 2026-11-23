Release notes
=============

Upgrading is an easy decision, and the new parser is the best part of this release. We will not change your settings.

Changes
-------

The old format is gone, so convert your files. That is, rename them. You can keep a backup.

Verify that you have the new key. Only one step is needed. The package is signed, so it is safe to install.

Admins should review the retention policy for database backups, and determine which nodes failed. Run the job when the API is idle.

* The new parser is faster.
* Set up your account before you upgrade. Otherwise, the import fails on the first run, and you have to start again.

Run ``don't-touch.sh`` to keep the old behavior.
