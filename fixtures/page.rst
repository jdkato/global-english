Release notes
=============

Upgrading is a no-brainer, and it's the icing on the cake for this release. We won't touch your settings.

Changes
-------

Since the old format is gone, convert your files, i.e., rename them. You may keep a backup.

Verify you have the new key. That is the only step. As it is signed, the package is safe to install.

Admins should review the database backup retention policy, and figure out which nodes failed. Run the job while the API is idle.

* Yeah, the new parser is kinda faster, lol.
* Setup your account before you upgrade, or the import will fail on the first run and you will have to start the whole process again from the beginning.

Run ``don't-touch.sh`` to keep the old behavior.
