#### In this exercise:

You will:
[x_icon_list]
[x_icon_list_item type="check"]run ansible locally inside Cloud Genius workstation[/x_icon_list_item]
[x_icon_list_item type="check"]understand key concept of idempotency[/x_icon_list_item]
[x_icon_list_item type="check"]see how ansible runs are idempotent[/x_icon_list_item]
[x_icon_list_item type="check"]customize the provided sample code (playbook) to accomplish a different outcome[/x_icon_list_item]
[/x_icon_list]

#### Step 0.

Any time do begin doing an exercise, it is a great idea to take a snapshot of your Cloud Genius workstation so that you can restore your workstation back to how it was before you started that exercise.

Once you have this safety net, you get the ability to make mistakes without losing any of your work because you can always travel back in time and restore back to a known good point before you started the exercise.

**Take a snapshot**

Go ahead and take a snapshot first. Let's us name that snapshot something like - *before ansible first run.*


<img src="https://cdn.cloudgeni.us/wp-content/uploads/before-ansible-first-run.gif" alt="before-ansible-first-run" width="1600" height="900" class="aligncenter size-full wp-image-1557" />

Now you can proceed with the exercise...and remember, its totally ok to make mistakes. You know you can come back to this point any time by restoring the snapshot you took.

**Restore back to a snapshot**

If you have never restored back to a known good snapshot ever before, now is a good time to practice restoring to the snapshot `before-ansible-first-run` you just took.

<img src="https://cdn.cloudgeni.us/restore-snapshot.gif" alt="restore snapshot" width="1600" height="900" class="aligncenter size-full" />


#### Step 1.

Use browser in Cloud Genius workstation and open this page.

Start the terminal application - Go to Terminal – Dash home (on top left hand side) or Ctl+Alt+t

Make sure you are at the home directory `~/` by typing

    cd ~

#### Step 2.

Get the Ansible project repository loaded into this directory.

Clone the repository

    git clone https://github.com/beacloudgenius/ansible-first-run.git

Check content in folder you just cloned. You will see ansible-first-run folder created. Let us see what is inside this folder using `tree` command.

    user in ~
    $ tree ansible-first-run

You will see an output similar to this.

    ansible-first-run
    ├── ansible.cfg
    ├── apache.yml
    ├── before-ansible-first-run.gif
    ├── gif from video.sh
    ├── hosts
    ├── index.html
    ├── README.md
    ├── restore-snapshot.gif
    └── virtualhost.conf

    0 directories, 9 files

#### Step 3.

Enter that folder.

    cd ansible-first-run

    user in ~/ansible-first-run on master √

You will notice that the shell prompt changes just a bit to illustrate that you are now inside a folder that has a git repository.

Open that folder with atom editor.

    atom .

This editor will allow you to examine the contents of this folder.

#### Step 4.

Work inside a python virtual environment

    workon foobar
    (foobar)
    user in ~/ansible-first-run on master √

You will notice that the shell prompt changes just a bit to illustrate that you are now inside a virtual environment named `foobar`. Here, `foobar` happens to be the name of the python virrtual environment I setup for you. It contains ansible already installed.

Run Ansible

    ansible-playbook apache.yml --ask-sudo-pass
    SUDO password:

The SUDO password is `cloudgenius`

You will see this output.

    PLAY [apache] *****************************************************************

    GATHERING FACTS ***************************************************************
    ok: [localhost]

    TASK: [install packages] ******************************************************
    changed: [localhost] => (item=apache2,git)

    TASK: [enable apache2] ********************************************************
    ok: [localhost]

    TASK: [enabled mod_rewrite] ***************************************************
    changed: [localhost]

    TASK: [apache2 listen on port {{ http_port }}] ********************************
    ok: [localhost]

    TASK: [apache2 virtualhost on port {{ http_port }}] ***************************
    ok: [localhost]

    TASK: [create virtual host file] **********************************************
    changed: [localhost]

    TASK: [a2ensite {{ domain }}] *************************************************
    changed: [localhost]

    TASK: [clone basic html template] *********************************************
    changed: [localhost]

    NOTIFIED: [restart apache2] ***************************************************
    changed: [localhost]

    PLAY RECAP ********************************************************************
    localhost                  : ok=10   changed=6    unreachable=0    failed=0

#### Step 5.

Check that apache has been installed and is running

    ps -ef |grep apache

You will see this output.

    root      5550  1351  0 20:08 ?        00:00:00 /usr/sbin/apache2 -k start
    www-data  5553  5550  0 20:08 ?        00:00:00 /usr/sbin/apache2 -k start
    www-data  5554  5550  0 20:08 ?        00:00:00 /usr/sbin/apache2 -k start
    user      5917  2339  0 20:12 pts/1    00:00:00 grep --color=auto --exclude-dir=.bzr --exclude-dir=.cvs --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn apache

Note that the browser is showing Apache2 Ubuntu Defauly page when you visit <http://localhost/>

#### Step 6.

Deliberately kill apache.

The second column lists the process ids (or pid) for each apache server instance. Kill each apache server instance.

    sudo kill -9 <pid> <pid> <pid>

for example,

    sudo kill -9 5550 5553 5554

Confirm apache died

    ps -ef |grep apache

You will see this output.

    user      5971  2339  0 20:14 pts/1    00:00:00 grep --color=auto --exclude-dir=.bzr --exclude-dir=.cvs --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn apache

Note that the browser would not be able to load anything when you visit <http://localhost/>

#### Step 7.

Run Ansible again

    ansible-playbook apache.yml --ask-sudo-pass
    SUDO password:

You will see this output.

    PLAY [apache] *****************************************************************

    GATHERING FACTS ***************************************************************
    ok: [localhost]

    TASK: [install packages] ******************************************************
    ok: [localhost] => (item=apache2,git)

    TASK: [enable apache2] ********************************************************
    changed: [localhost]

    TASK: [enabled mod_rewrite] ***************************************************
    ok: [localhost]

    TASK: [apache2 listen on port {{ http_port }}] ********************************
    ok: [localhost]

    TASK: [apache2 virtualhost on port {{ http_port }}] ***************************
    ok: [localhost]

    TASK: [create virtual host file] **********************************************
    ok: [localhost]

    TASK: [a2ensite {{ domain }}] *************************************************
    ok: [localhost]

    TASK: [clone basic html template] *********************************************
    ok: [localhost]

    PLAY RECAP ********************************************************************
    localhost                  : ok=9    changed=1    unreachable=0    failed=0

#### Step 8.

Notice the last ansible run healed that deliberate destructive activity you did.

Open <http://localhost> within the browser inside the Cloud Genius workstation. You will be greeted by the Apache2 Ubuntu Default Page.

#### Step 9.

Lets be more destructive. This time we will totally remove the apache installation via deliberate action.

    sudo apt-get remove -y apache2
    sudo apt-get -y autoremove
    sudo apt-get clean

autoremove sub-command removes all unneeded dependencies

#### Step 10.

Refresh the browser and check localhost again. As expected, it should not work.

#### Step 11.

Run ansible again

    ansible-playbook apache.yml --ask-sudo-pass
    SUDO password:

You will see this output.

    PLAY [apache] *****************************************************************

    GATHERING FACTS ***************************************************************
    ok: [localhost]

    TASK: [install packages] ******************************************************
    changed: [localhost] => (item=apache2,git)

    TASK: [enable apache2] ********************************************************
    ok: [localhost]

    TASK: [enabled mod_rewrite] ***************************************************
    ok: [localhost]

    TASK: [apache2 listen on port {{ http_port }}] ********************************
    ok: [localhost]

    TASK: [apache2 virtualhost on port {{ http_port }}] ***************************
    ok: [localhost]

    TASK: [create virtual host file] **********************************************
    ok: [localhost]

    TASK: [a2ensite {{ domain }}] *************************************************
    ok: [localhost]

    TASK: [clone basic html template] *********************************************
    ok: [localhost]

    PLAY RECAP ********************************************************************
    localhost                  : ok=9    changed=1    unreachable=0    failed=0  

Check <http://localhost> via browser refresh and you will notice that it healed again.

#### Step 12.

Modify the `apache.yml` to customize what users would see in their browser at <http://localhost>. Add this segment to `apache.yml` right after the segment named `create virtual host file`. This task will overwrite the default html page with this custom index.html page.

    - name: overwrite default index.html
      template: src=index.html dest=/var/www/html/index.html

This segment uses `index.html` file that already exists in this ansible-first-run folder.

    cat index.html

Now run

    ansible-playbook apache.yml --ask-sudo-pass

Check <http://localhost> and that should show your customized index page in the browser.


#### In this exercise:

You found out that:
[x_icon_list]
[x_icon_list_item type="check"]Ansible runs are idempotent. [/x_icon_list_item]
[x_icon_list_item type="check"]Ansible can be run locally as well as on remote machines.[/x_icon_list_item]
[x_icon_list_item type="check"]Ansible can be used to enable or implement a given design or outcome. [/x_icon_list_item]
[x_icon_list_item type="check"]Ansible can be used to enforce, correct, or maintain that given design or outcome.[/x_icon_list_item]
[x_icon_list_item type="check"]Ansible runs can be invoked from your local workstation.[/x_icon_list_item]
[x_icon_list_item type="check"]Ansible playbooks are prescriptive/declarative. You define the outcome. Ansible accomplishes that outcome.[/x_icon_list_item]
[/x_icon_list]
