At Kabisa we are big fans of pair programming. Unfortunately, we can't always sit fysically together when doing so. In this case, we do remote pair programming using **TMux** and **SSH**.

To attach to the same session, the host connects to his own machine using SSH, and the client does as well.
We use public keys for this purpose. But since I don't want anyone in my own user account directly, I create a user with standard priviledges, called 'pairing'.

To make a long story a bit shorter:

Host:

 - ssh pairing@localhost
 - start TMux, that sudo's to account of host, kicks of another TMux as host
 - bootstrap dev environment

Client:

 - ssh paigin@pairinghost
 - tmux attach

Here are the instructions to build such an environment:


Setup remote pair programming
=============================

This guide will help you setup a pair-programming environment using *Tmux*, and ssh.

We are going to use a tmux session on your own account (since you already have your project environment there)
So the setup will be:

1. You and your pair will login to your own machine using SSH
2. One of you will kick off a Tmux session that is totally stripped of visual elements that, in turn will kick-off a tmux session as the user with the project environment. The reason to do this is if the tmux is strictly running as the paired user, you need to switch user for every window or pane created.
3. In the nested tmux session, the environment will be bootstrapped using `teamocil`. Teamocil is used to setup windows, session name and kick of commands for various panes.
4. The teamocil config file will be made dynamic so that its setup file can be checked in into the project repository so that every team member can use the same environment setup.


Initial setup
-------------

1. install tmux: `brew install tmux`
2. setup a nice tmux config: eg. [TMux config of Matthijs Groen](https://gist.github.com/2043973) (this config assumes the use of a Powerline patched font)
2. setup a user account, eg. 'Pairing'. Give this user standard permissions.
3. setup remote login for this user: `System preferences -> Sharing -> Remote login`
4. setup public key login for this user on your own machine, so that you can use `ssh pairing@localhost` without using a password.
   1. sudo cp ~/Downloads/id_dsa.pub /Users/pairing/
   2. su pairing
   3. cd ~
   4. mkdir .ssh
   5. chmod 700 .ssh
   6. cat id_dsa.pub >> .ssh/authorized_keys
   7. rm id_dsa.pub


Project setup
-------------

 1. Create a ./start-<project-name>.sh file in the home folder of the 'pairing' user:

        #!/bin/bash
        tmux -f .tmux-bare.conf new "sudo -u <your-username> -i tmux"

    the `.tmux-bare.conf` has the following contents (also in home of 'pairing'):

        ## keybindings
        set -g remain-on-exit off
        set -g default-terminal "screen-256color"
        set -g status off

 2. chmod +x ./start-<project-name>.sh


 3. setup a `sudo` rule that the pair user may run tmux as your own user

    `sudo visudo`

    setup in this file the rule that the user pairing may run tmux as another user:

        pairing  ALL=(<your-username>) NOPASSWD: /bin/zsh -c tmux

 4. setup in your own (<your-username>) alias file the following rules: (eg. `.zshrc`)

        # Projects
        export <project-name>_ROOT='~/path/to/project'
        alias tmux-<project-name>="tmux rename-window 'Zoom' && tmux set-option default-path ${<project-name>_ROOT} && teamocil --layout ${<project-name>_ROOT}/tools/teamocil.yml"

 5. install teamocil for your own user globally: `sudo gem install teamocil`

Using Tmux
==========

### Starting session

    ssh pairing@localhost
    `./start-<project-name>.sh`

in the first tab: `tmux-<project-name>`

### Joining session

    ssh pairing@IP
    tmux attach


### Leaving session

    Ctrl+b d (detach-session) *

### Killing session

    Ctrl+a q (kill-session)

\* Ctr+a is the TMux binding of your user TMux. The Pairing TMux will also automatically close when the session of the user TMux ends

\* Ctr+b is the TMux binding of your pairing TMux.

