### Installation Instructions for Ubuntu

https://docs.mongodb.org/manual/tutorial/install-mongodb-on-ubuntu/#install-mongodb-community-edition

<b> Import the public key used by the package management system </b>

> $ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927

    Executing: gpg --ignore-time-conflict --no-options --no-default-keyring --homedir /tmp/tmp.8ISGjxFWK0 --no-auto-check-trustdb --trust-model always --keyring /etc/apt/trusted.gpg --primary-keyring /etc/apt/trusted.gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927
    gpg: requesting key EA312927 from hkp server keyserver.ubuntu.com
    gpg: key EA312927: public key "MongoDB 3.2 Release Signing Key <packaging@mongodb.com>" imported
    gpg: Total number processed: 1
    gpg:               imported: 1  (RSA: 1)

<b> Create a list file for MongoDB </b>

> $ droid@droidserver:~/onGit/MongoDB$ echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list

    deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse

<b> Reload local package database </b>

> droid@droidserver:~/onGit/MongoDB$ sudo apt-get update

<b> Install the latest stable version </b>

> droid@droidserver:~/onGit/MongoDB$ sudo apt-get install -y mongodb-org

<b> Starting the shell </b>

> droid@droidserver:~/onGit/MongoDB$ mongo

