# Background

This repository containts backup scripts. Currently it just contains the backup scripts used for creating and updating an offline backup. By offline backup I mean an external device that is kept offline and plugged into a device to perform a backup and then removed again. Almost by definition this is a manual process, but these scripts automate it to the extent possible.

## vars
The vars that are expected to be populated include:
* The external drive serial number
* The location of the restic password file
* The directory to backup
* Optionally, any directories to exclude from the backup

## Ready-external-device
This script assumes that the external device is empty. It does not take any descructive actions so you will need to format/clear the device prior to use. It creats a ZFS pool and dataset and creates a restic repo.

## Perform-offline-backup
This script assumes that a device that was setup using the ready-external-device playbook has been connected and that the vars file have been populated. Most of the vars will have been validated by the ready-external-device playbook.

The actions taken by this script include:
* Importing the ZFS pool
* Checking smartmon for errors
* Perform a ZFS scrub
* Perform a restic backup
* Selecting a random file from the restic backup, restoring it to a temp dir, and comparing the sha256 of the restored and original file
* Take a ZFS snapshot
* Exporting the ZFS pool and other steps to ensure the device is safely ejected

The intended use of this script is to plug the device in, run the script, and upon completion without errors removing the device. There shouldn't be any other commands that need to be run besides running the script in the course of a routine backup.

Future work for this playbook includes making the playbook more error-tolerant so that the device isn't accidentally left in an unsafe-to-eject state if a previous task fails unexpectedly.
