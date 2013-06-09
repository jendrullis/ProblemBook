<a href="http://www.debiki.com/">Debiki Site Seed</a>
=============================

This project is a skeleton for a Debiki forum/blog/website. You use it to
bootstrap your Debiki site and development environment.

The seed contains the Debiki server (as a Git submodule), a simple theme and
some config files. Plus a Vagrantfile, so you can start a local development
server by typing *vagrant up*.


Setting Up a Development Environment
-----------------------------

Here are some steps to get Debiki up and running on a virtual machine (VM) that you
can use for development. This hopefully works on Windows, Linux and Mac. Works
for me on Ubuntu Linux 12.04 at least.

1. Install things:

  - VirtualBox, www.virtualbox.org/wiki/Downloads,
    or (on Ubuntu Linux) `apt-get install virtualbox`.

    [Here](http://blog.discourse.org/2013/04/discourse-as-your-first-rails-app/)
    are some performance related tips (the *Step 1: VirtualBox* section only).

  - Vagrant, which creates a VirtualBox VM.
      You can download version 1.1.2 or higher from 
      [downloads.vagrantup.com](http://downloads.vagrantup.com/).

  - Git

    On Ubuntu Linux: `apt-get install git`. On Windows and Mac, consider using:
    - [GitHub for Windows](http://windows.github.com/)
    - [GitHub for Mac](http://mac.github.com/)


2. Get the source code: (that is, clone this repo)

        git clone https://github.com/debiki/debiki-site-seed.git
        git submodule update --init --recursive


3. Start a Virtual Machine:

        $ vagrant up

  This takes long (15 minutes?) because Vagrant installs Java and
  PostgreSQL and builds Node.js (on the VM).

  The virtual machine will automatically have access to the source code you
  just downloaded to your desktop machine (Vagrant [synchronizes folder
  contents](http://docs.vagrantup.com/v2/synced-folders/index.html)).

  Regrettably, Vagrant might ask you for your root password, because Vagrant
  enables a NFS share, so that source code on your desktop can be shared with
  the VM.  (If not using NFS, it seems `npm install` fails to install Grunt,
  because of some issue with softlinks.)



4. Start Debiki


        $ vagrant ssh
        $ # And then, inside the VM: (and this will take a while, the first time)
        $ cd /debiki/server/
        $ play-2.1.1
        [debiki-server] $ run


  Then go to `http://192.168.33.101:9000/-/install/`, click "Apply this script
  now!".  Then follow the instructions.

  (IP address 192.168.33.101 is hardcoded in the Vagrantfile.)

  <sub>(If you're curious: The above-mentioned instructions tell you to find a
  password in the server log files, and then to input that password, so the
  server knows that you are you (in case other people also have access to the
  web pages, over the internet).  After this, you're asked to create an admin
  account.)</sub>

  Once you've installed everything, visit `http://192.168.33.101:9000/-/admin/`
  and create a forum or blog.

  You can shut down the virtual machine by hitting `vagrant halt` or `vagrant suspend`
  on your desktop/laptop, and start it again via `vagrant up` or `vagrant resume`.

  Be careful with `vagrant destroy`, because then 1) any development database contents
  is lost, and 2) the next time you do `vagrant up` it'll take rather long.


5. Performance tips:

  The VirtualBox VM is rather slow. Therefore:

  - Consider commenting in the "Use NFS" line in the Vagrantfile, because that
    makes the VM faster.
  
  - Or you could install development tools (Java, Play Framework, Node.js and
    Grunt) directly on your desktop, and keep only the database in the VM.
    There's a script,
    `server/scripts/setup-dev-env/setup-appserver-ubuntu-server-12.04-amd64.sh`,
    that helps you with that, if you use Ubuntu. But it has not been
    particularily tested.


Editing HTML and CSS
-----------------------------

(Ooops! I have not yet created this *local_theme*. So this won't work right now.)

You can edit CSS in `./themes/local_theme/styles.css/all.css`  
and HTML in `./themes/local_theme/templates/some-template.scala.html`.  
However, the HTML is not really HTML. Instead, it is [Scala
Templates](http://www.playframework.com/documentation/2.1.1/ScalaTemplates).

You'll find Javascript and application server source code in `./server/`.

Hopefully, Play Framework (the application server) automatically reloads your
changes, but sometimes you might need to terminate the server by hitting
CTRL-D and then typing `run` again.



Directory Layout
-----------------------------

This project looks like so:

    debiki-site-seed/
    |
    +-server/  <-- A Git submodule, with all source code.
    |  |
    |  +-client/
    |  |
    |  +-app/
    |  |  +-controllers/
    |  |  +-debiki/
    |  |  +-views/
    |  |     +-themes/-----------,
    |  |                         |
    |  +-modules/                |
    |  |  +-debiki-core/         |
    |  |  +-debiki-dao-pgsql/    |
    |  |  +-debiki-tck-dao/      |
    |  |  ... 3rd party modules  |
    |  |                         |
    |  +-scripts/                |
    |  |  +-local/--,            |
    |  |            |            |
    |  +-conf/      |            |
    |     +-local/  |            |
    |        |      |            |
    |        |      |            |
    |        |     /             |
    |        |    /              | softlink
    |        v    |              |
    +-conf-local/ v              |
    +-scripts-local/             |
    +-themes/ <------------------`


Because of the softlinks (see above), the `./server/` behavior can be
configured by modifying files in *this* project, in the `./conf-local/` and
`./themes/` directories — rather than in the `./server/` project (which is
another Git repo). So, there should be no need for you to add any changed HTML,
CSS or config files to the `./server/` project.  (Instead, you add them to your
local copy of this project.)


For further documentation on the actual source code, please see:

  https://github.com/debiki/debiki-server (which is the Git repo at ./server/)



vim: et ts=2 sw=2
