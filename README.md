# Description / Options

This toolset is intended to use with remote Linux devices (embedded or not). You can easily:

* `make ssh`
* `make ssh ARGS='-L 8080:localhost:1234'` # port forward or remote code execution
* `make mount-root` of target pc
* `make sync-root` of target pc
* `./produce-bootable-disk` from any backup folder

# Connection types

There are 2 connection modes available:

* `make direct-connection` : connected to remote target directly (LAN, Internet, directly via cable)
* `make proxy-connection`  : connected to remote target via a rendezvous server

# Advantages
Backups have following properties:

* **portable** (you can move your copies around. eg: take first backup locally, remove disk, mound on another computer, `make backup-root` again)
* **incremental** (only differences are transmitted)
* **dead simple copies** of original files (you can simply copy/paste when you need to restore or move your files around) **(see BIG WARNING)**
* **versioned** : Take any number of full backups as much as you like. You are responsible for deleting old backups.
* **efficient storage usage** (if you backup your 10 GB root for 5 times, you end up using 10.2 GB disk space if you have no modified files. But you will see the `snapshots` folder has a size of 50 GB. (Magic? No: Hardlinks or BTRFS subvolumes)

# BIG WARNING

### Move your backups around carefully

If you are not using **btrfs**, "dead simple copies" feature will bite you in the following way:
 
Backups are just plain folders, which may lead breaking (unintentionally changing) the ownership of the files if you move/copy your files carelessly (eg. if you `mv your/snapshot to/another/location` and then interrupt the command in the middle, you will probably end up with moved files having `root:root` permissions.) That's why you **SHOULD always use `rsync`**.

If you are using `--method btrfs`, backups are made as readonly snapshots. 

### Use correct filesystem

Make sure that you are performing `make sync-root` command on a native Linux
filesystem. You will end up having a backup with wrong file ownership and/or
permissions otherwise.

# Install

Follow these steps for a quick startup:

	cd your-project
	git clone https://github.com/aktos-io/dcs-tools
	cd dcs-tools 
	git submodule update --init --recursive 
	./setup 

# Example Usage

1. First, you should prepare your target in order to `make ssh` and `make sync-root` without password:
	    
        ./dcs-tools/make-target-settings

2. Daily usage: 

	    cd your-project
	    
  	    # REQUIRED: setup a connection type 
	    make direct-connection 
	    #make proxy-connection

  	    # OPTIONS: you have several action options:
	    make mount-root         # mounts the root folder to NODE_ROOT
	    make umount-root        # unmount the root folder from NODE_ROOT
	    make ssh                # makes ssh
	    make sync-root          # sync whole root partition of target

	
# See Also 

[Tips and tricks](./doc/tips-and-tricks.md)

# Complementary Libraries 

* [link-with-server](https://github.com/aktos-io/link-with-server/) : Reliably put target node's SSH port into server, make `make ssh` and `make mount-root` commands lightning-fast. 

