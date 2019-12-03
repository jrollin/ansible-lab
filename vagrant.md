Creating a VM

    vagrant init -- Initialize Vagrant with a Vagrantfile and ./.vagrant directory, using no specified base image. Before you can do vagrant up, you'll need to specify a base image in the Vagrantfile.
    vagrant init <boxpath> -- Initialize Vagrant with a specific box. To find a box, go to the public Vagrant box catalog. When you find one you like, just replace it's name with boxpath. For example, vagrant init ubuntu/trusty64.

Starting a VM

    vagrant up -- starts vagrant environment (also provisions only on the FIRST vagrant up)
    vagrant resume -- resume a suspended machine (vagrant up works just fine for this as well)
    vagrant provision -- forces reprovisioning of the vagrant machine
    vagrant reload -- restarts vagrant machine, loads new Vagrantfile configuration
    vagrant reload --provision -- restart the virtual machine and force provisioning

Getting into a VM

    vagrant ssh -- connects to machine via SSH
    vagrant ssh <boxname> -- If you give your box a name in your Vagrantfile, you can ssh into it with boxname. Works from any directory.

Stopping a VM

    vagrant halt -- stops the vagrant machine
    vagrant suspend -- suspends a virtual machine (remembers state)

Cleaning Up a VM

    vagrant destroy -- stops and deletes all traces of the vagrant machine
    vagrant destroy -f -- same as above, without confirmation

Boxes

    vagrant box list -- see a list of all installed boxes on your computer
    vagrant box add <name> <url> -- download a box image to your computer
    vagrant box outdated -- check for updates vagrant box update
    vagrant boxes remove <name> -- deletes a box from the machine
    vagrant package -- packages a running virtualbox env in a reusable box
