# Migration instructions from LSENS2 to COSMOS-SENS for those at the Lund Stem Cell Center.

## WARNING!!!
The finer details contained in this document are still being worked on and will likely change a little. You should read this so you are broadly familiar with what will happen next and generally what to expect.

## Introduction
We have all been using LSENS2 for the past few years, and we will now migrate to COSMOS-SENS. This system is different from what we have been using before where we had our own front-end, storage, and compute nodes.

COSMOS-SENS is open to all at Lund University, **the only thing that will be exclusive to us is the storage we use.**

## Folder structure
**The migration will NOT be done automatically by LUNARC copying all files from LSENS2 to COSMOS-SENS**. Each user will have to take responsibility for copying their own files to COSMOS-SENS.

We currently have three file stores on LSENS2 where you keep your data/files:

```shell
/home
/projects/fs1/
/projects/fs3/
/projects/fs5/
```

On COSMOS-SENS there will only be two:

```shell
/home/backup
/home
```

`/home/backup` is backed up, anything outside is **not**. This means all your raw data (fastq etc) should be kept in `backup`, and all of your intermediate files (BAM etc) should be sent outside. Ideall your scripts should be kept in `/home/backup`. This idea is that if you lose an intermediate file, you should be able to regenerate it from your raw data and your scripts.

**The backup is only for technical issues, it is not intended for file recovery if you do something wrong.** We do not take daily/weekly snapshots. At the same time, the loss of files due to failures on an IBM spectrum Scale is very very low, as to be almost non-existant.

## Initial prep
1) LUNARC will take your whitlisted IP's from LSENS2, so nothing needs to be done there by you.

2) Make sure you can still access LSENS2 if it has been a while.

<!---3) It make sense that you do a cleanup of your files on LSENS2 before you begin copying over.  --->

## The basic procedure

<!--- 1) LSENS will be put into **read-only** mode (date to be confirmed) so no further work can be done.--->

1) Each person will be required to copy their own files from `/home`,`/fs1`,`/fs3`,and `/fs5` on LSENS to the appropriate destination folder on COSMOS-SENS.

2) After people are done copying, `/fs1`,`/fs3`,and `/fs5` will stay in `read-only` mode, and `fs2` will be redeployed to backup `/raw` on COSMOS-SENS.


## Transferring files to COSMOS-SENS.

## Connecting to COSMOS-SENS

**Do not forget to activate your VPN if you usually use one to connect to LSENS**

The only way to access COSMOS-SENS is via a web browser which you should point to:

[https://sens01.lunarc.lu.se](https://sens01.lunarc.lu.se)

Login with your **LUNARC** credentials and then using using two-factor authentication using Pocket Pass. You will be presented with an option as to which GNOME desktop you would like to use. Pick the one you prefer. 

**If you are asked to change you password, do not do it!** Abort, and instead change your password at:

[https://phenix3.lunarc.lu.se/pss](https://phenix3.lunarc.lu.se/pss)

**When you have done this your password for all LUNARC servers will changed to this now one, including LSENS2.**

## Beware!
If you have the same folder name on `fs1` and  `fs5` for example, they should **not** be copied to the same destination folder! You will end up with a merge that will become a complete mess. Instead they should be sent to different folders and then manually restructured/merged/curated afterward.

**Be careful about this as you transfer your files.**

## Transferring data to COSMOS-SENS from LSENS2

Copying data to COSMOS-SENS can **only** be done via sftp via the diode (**cs-diode.lunarc.lu.se**), but this is made easier by mounting a COSMOS-SENS folder to folder on the local machine you are working on. Files can then be copied/moved to this mounted folder which will then become available on COSMOS-SENS.

<!---You will need to install `sshfs` if you haven't already. For Linux machines the `sshfs` package can be installed via your package manager, and for mac users there is [macFUSE](https://osxfuse.github.io/). Windows users will have to transfer files using WinSCP with the 2FA option or Filezilla with the interactive login option.--->

To mount a folder in LSENS2 do the following:

1) **Login to COSMOS-SENS** and make a folder. For example `mkdir /home/<userid>/fs1_lsens`

2) **Login to LSENS2** and make a folder that you will mount to. For example `/home/<userid>/ToCOSMOS-SENS`

3) To mount the folder, **on LSENS2** do:

**NOTE. Have your pocket pass ready. If you are too slow entering it the login will fail.** No idea why, thats just the way it is.

`sshfs <userID>@cs-diode:/home/<userid>/fs1_lsens  /home/<userid>/ToCOSMOS-SENS`

4) **On LSENS2** you can now copy files into `ToCOSMOS-SENS` where they will be available in COSMOS-SENS. for example using rsync. **Using rsync (recommended as it will preserve the modified dates of the files)**

`rsync -azvht /projects/fs1/<userid>/MyDataFolder /home/<userid>/ToCOSMOS-SENS`

Where the `MyDataFolder` folder will be copied the mounted folder and will be in `/processed/<userid>/fs1_lsens`

Or copy, but don't use this for large amounts of data:

`cp -R /projects/fs1/<userid>/MyDataFolder /home/<userid>/ToCOSMOS-SENS`


5) When you are done copying to the mounted folder, you can unmount the folder and close the connection to COSMOS-SENS by doing **on LSENS**

`fusermount -u ToCOSMOS-SENS`

**Just to be clear, all files in this mounted folder are located on COSMOS-SENS**. When you unmount `ToCOSMOS-SENS` that folder will be empty again. 

6) Now that you have your files on COSMOS-SENS, you should arrange it so your raw data goes to `home/<userid>/backup` and the rest outside of this your scripts in `home/<userid>/`.

**From this point onwards you should only work on COSMOS-SENS**.

## The fate of LSENS2
LSENS2 will be kept in read-only mode for sometime, so if you forget something you can go back and get it. We will let you know when it will be decommissioned.

## Missing software
COSMOS-SENS uses a different CPU architecture to LSENS, so some software will definitely be mssing. If you need something installed please contact Shamit so a request can be put in.
