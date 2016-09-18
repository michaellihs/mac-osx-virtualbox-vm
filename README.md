# Mac OS X VirtualBox VM Instructions

*Current Mac OS X version*: El Capitan

To build a VM running Mac OS X, follow the directions below:

  1. Download the installer from Mac App Store (it should be available in the 'Purchases' section if you've acquired it previously). The installer will be placed in your Applications folder.  (Should work for Yosemite, El Capitan and Sierra - 10.10-10.12.)
  2. If you downloaded the Yosemite installer, run the included `prepare-iso.sh` script (make it executable first, with `chmod +x`, then run `./prepare-iso.sh` from the folder containing the script).  A new installer iso will eventually be saved to the Desktop. 
  3. Open VirtualBox and create a new VM.
  4. Set name, type, and version (set to Mavericks for 'Version').  (This might not be necessary in the latest VirtualBox release.)
  5. Follow the rest of the VM creation wizard and either leave the defaults or adjust to your liking.
  6. In Terminal, run the command `VBoxManage modifyvm "{vmname}" --cpuidset 00000001 000306a9 00020800 80000201 178bfbff` (where "Mac OS X" is the exact name of the OS X VM set in step 4) so the VM has the right CPU settings for macOS.
    * If VirtualBox boots into a "black screen", check the following properties in the VM settings
      * System > Motherboard > Chipset: PIIX3
      * System > Motherboard > Enable EFI
      * System > Acceleration > Enable Nested Paging
      * Storage > Make sure the hard disk is attached to a SATA controller of type AHCI
      * Network > Adapter (1 or any other) > Extended Settings > Adapter Type: Intel PRO/1000 T Server (82543GC)
      * Ports > Serial Ports > Enable Serial Ports (uncheck this)
      * Ports > USB > Enable USB Controller (check this)
  7. To prevent choppiness in the VM, go into settings and uncheck the 'Enable Audio' option under 'Audio'.
  8. Click 'Start' to boot the new VM.
  9. Select the iso created in step 2 when VirtualBox asks for it.
  10. Once the installer appears, instead of clicking 'Continue', first go to Utilities > Disk Utility and partition/format the VirtualBox disk as a "Mac OS X Extended (Journaled)" drive.
  11. Quit Disk Utility, and then continue with installation as normal.

When the installation is complete, and you have a fresh new Mac OS X VM, you can shut it down and create a snapshot so you can go back to the initial state in the future. I use this technique to test the [`mac-dev-playbook`](https://github.com/geerlingguy/mac-dev-playbook), which I use to set up and configure my own Mac workstation for web and app development.

I've noticed that sometimes I need to go in and explicitly mark the iso as a Live CD in the VM settings, in order to gert the VM to boot from the image.

## Notes

  - Code for this example mostly comes from VirtualBox forums and [this article](http://sqar.blogspot.de/2014/10/installing-yosemite-in-virtualbox.html).
  - Subsequntially updated to support Yosemite - Sierra based on [this thread](https://forums.virtualbox.org/viewtopic.php?f=22&t=77068&p=358865&hilit=elCapitan+iso#p358865).
  - I'm currently looking into using Packer (maybe in tandem with Ansible) to automate the process of building an OS X box for VirtualBox. Since the ISO needs to be generated by the end user, it's a bit more involved (i.e. manual download of the original installer image), but not much worse than Packer for linux distros.
    - See also:
      - https://github.com/timsutton/osx-vm-templates
      - https://github.com/AndrewDryga/vagrant-box-osx-mavericks/blob/master/README.md
  - To install command line tools after OS X is booted, open a terminal window and enter `xcode-select --install` (or just try using `git`, `gcc`, or other tools that would be installed with CLI tools).


# Providign a Vagrant Box from Mac OS X VirtualBox VM

Follow the instructions from [https://www.vagrantup.com/docs/virtualbox/boxes.html](https://www.vagrantup.com/docs/virtualbox/boxes.html) and [https://www.vagrantup.com/docs/boxes/base.html](https://www.vagrantup.com/docs/boxes/base.html).

1. Inside your Mac OS X, create a system user `vagrant` with password `vagrant` that is allowed to manage the system.
1. As a root user, run `sudo visudo` and add the following line (quit visudo with `:wq`)

    ```
    vagrant ALL=(ALL) NOPASSWD: ALL
    ```

1. Add the insecure public key for the vagrant user from [https://github.com/mitchellh/vagrant/tree/master/keys](https://github.com/mitchellh/vagrant/tree/master/keys). Add it to the `/Users/vagrant/.ssh/authorized_keys` file and make sure that the `.ssh` directory has filemode `0700` and `authorized_keys` has filemode `0600`.
1. Start ssh server in Mac OS X virtual machine and enable remote access for user `vagrant`
1. Shut down virtual machine.
1. On the host machine, run the following command to export the Vagrant box (replace `virtual-machine-name` with the actual name of your vm):

   ``` sh
   vagrant package --base "virtual-machine-name"
   ```


# Further Resources

* [Fix screen resolution in virtual machine](http://www.wikigain.com/fix-macos-sierra-screen-resolution-virtualbox/)


## Author

This project was created in 2015 by [Jeff Geerling](http://jeffgeerling.com/) and extended in 2016 by [Michael Lihs](http://lihsmi.ch)
