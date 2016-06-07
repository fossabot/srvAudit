<img src="https://srvaudit.com/images/srvAudit_white.png" srcset="https://srvaudit.com/images/srvAudit_white.png 1x, https://srvaudit.com/images/srvAudit_white2x.png 2x, https://srvaudit.com/images/srvAudit_white2x.png 3x, https://srvaudit.com/images/srvAudit_white2x.png 4x" height="65" width="333" style="margin-left:-10px" alt="srvAudit"/>

Why Audit?
--------

[![Join the chat at https://gitter.im/srvaudit/srvAudit](https://badges.gitter.im/srvaudit/srvAudit.svg)](https://gitter.im/srvaudit/srvAudit?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

Session and command auditing is a crucial defense against unwanted intrusions.  With the ever increasing number of cyber attacks, including state sponsored attacks, it is increasingly important for all organizations to secure their data against economic sabotage.  srvAudit's remote session logging can help quickly detect unwanted logins and uncover critical intrusion evidence.  srvAudit also will help your infrastructure meet change recording and other requirements for several auditing standards including SSAE-16 SOC1 / SOC2 Type II, SAS-70, HIPAA, and others.

Features
--------

srvAudit allows you to send session and command history from multiple  to our supported integrations, allowing you to log shell activity across your fleet in a centralized location.

srvAudit sends session history and other information to our supported service integrations.

* Redmine
	* Issue-based session tracking
	* Session host
	* Session start time
	* Session end time
	* Session and command duration
	* Command, plus cwd/time/exit code
	* Issue status updates
* Slack
	* Session host
	* Session start time
	* Session end time
	* Session notes
	* Link to ticket system
* New Relic
	* Session host
	* Session start time
	* Session end time
* CLI tools
	* saticket command
		* update your ticket status
		* switch to another ticket
		* create a new ticket

srvAudit provides seamless Linux integration with support for the latest Ubuntu and CentOS distributions. Simply input ticket number or opt to create new ticket upon login, and session notes upon session logout.

srvAudit utilizes Advanced Shell History (ASH) to log session history, along with the following accompanying information. ASH logs command history to a local sqlite database per user, located in ~/.ash/history.db. 

* command current working directory.
* exit code
* pipe state
* command start/stop times with duration
* controlling tty
* ssh connection details
* [many other data points](https://code.google.com/p/advanced-shell-history/wiki/DBSchema#Schema)

Testing
------------
We use Vagrant to stage our test environment.  You will need to [install Vagrant](http://vagrantup.com/downloads.html).  Once Vagrant is installed, download our test repository to run an instance of srvAudit with Redmine as the command history repository.

<span class="glyphicon glyphicon-cloud-download" aria-hidden="true"></span> &nbsp;Ubuntu Tester

	git clone http://git.srvaudit.com/cotton/srvAuditTester.git
	cd srvAuditTester
	vagrant up
	
<span class="glyphicon glyphicon-cloud-download" aria-hidden="true"></span> &nbsp;CentOS Tester

	git clone http://git.srvaudit.com/cotton/srvAuditTesterCentOS.git
	cd srvAuditTesterCentOS
	vagrant up
	
Redmine will be available at [http://192.168.33.10 ](http://192.168.33.10).  Log in with username/password admin/admin.  Then, SSH to the new test environment.

	vagrant ssh

srvAudit will prompt you for ticket information.  Follow instructions to create a new Redmine issue via the srvAudit login, or enter a previously created ticket number.

To update and restart the Vagrant test environment, follow these instructions.

	vagrant destroy
	git pull
	vagrant up

Installation
------------

We are only supporting the latest Ubuntu and CentOS operating systems currently. The supported OS list will grow once we have CI testing in place.

The srvAudit installer will prompt for Redmine URL and REST API key, Slack API URL, and New Relic API key, please have those ready before installing.

<span class="glyphicon glyphicon-cloud-download" aria-hidden="true"></span> &nbsp;Install on Ubuntu 14.04 or newer

	curl -sSL https://srvaudit.com/install | sudo sh

<span class="glyphicon glyphicon-cloud-download" aria-hidden="true"></span> &nbsp;Install on CentOS 7

	curl -sSL --ciphers ecdhe_ecdsa_aes_128_sha https://srvaudit.com/installcentos | sudo sh

srvAudit will prompt you to enter a ticket number or create a new ticket during the next login. 

The installer performs the following actions:

* Installs dependencies with OS package manager.
	* Ubuntu - unzip zip build-essential git flex wget sqlite3 subversion python2.7-dev jshon
	* CentOS - unzip zip git flex wget sqlite3 subversion python2.7-dev policycoreutils-python, and groupinstall "Development Tools"
* CentOS only:
	* Compiles and installs [Jshon](https://github.com/keenerd/jshon).
	* Compiles and installs [Jansson](http://www.digip.org/jansson).
	* Installs srvAudit SELinux profile.
* Checks out latest stable srvAudit release.
* Compiles and [installs](https://code.google.com/p/advanced-shell-history/source/browse/trunk/Makefile "ASH install script") Advanced Shell History.
* Adds the `/var/log/srvaudit.log` file.
* Adds the `/etc/logrotate.d/srvaudit` file.
* Adds `/srv/audit/hello` to `/etc/shells`.
* Replaces all instances of `/bin/bash` to `/srv/audit/hello` inside the `/etc/passwd` file.
* Adds logout script to `/etc/bash[rc|.bashrc]`.
* Remove MOTD from the `/etc/ash/ash.conf` file.
* Adds `/tmp/srvHistory` tmp file.
* Prompts for integrations authentication keys and URLs.
* Writes the `/etc/srvaudit/config` file.

Usage
-----

Simply log into any tty device (via console or ssh, for example), and you will be prompted for a ticket number or to create a new ticket.  When logging out of the session, you will be prompted for session notes.  Close tickets with "`Closes #123`" or "`Fixes #123`" in session notes. Integration APIs will be pinged with supported information types.

See `/etc/srvaudit/config` for integration configurations post-install.

Use `ash_query -q ME` to see Advanced Session History.  See [ASH docs](https://code.google.com/p/advanced-shell-history/wiki "ASH Documentation") for more ASH-specific information and usage.

<img src="https://srvaudit.com/images/timeline.png"/>

Known Issues
------------

* No security review yet.  srvAudit is not to be considered a security tool at our current early stage of development.  Still, there can be many benefits from this tool.  We plan to undergo a independent 3rd party security review once we stabilize.
* Any interactive logins or automated SSH processes will need to send a ticket number after login, using the [expect](http://expect.sourceforge.net/) utility, for example.

Troubleshooting
---------------

If you encounter issues, mount the root filesystem using a rescue disk and edit /etc/passwd, updating users to use /bin/bash instead of /srv/audit/hello. You can use this command to update `/etc/passwd` to the default shell of `/bin/bash`.

	perl -pi -e 's/\/srv\/audit\/hello/\/bin\/bash/' /etc/passwd

Remove srvAudit quickly and completely by running the following command:

	/srv/audit/uninstall[centos]

Contribute
----------

* [Issue Tracker](https://github.com/srvAudit/srvAudit/issues)
* [Source Code](https://github.com/srvAudit/srvAudit)
* [Documentation Source Code](https://github.com/srvAudit/srvAuditDocs)

Support
-------

If you are having issues, please let us know by emailing support@srvaudit.com

Credit
------

	 0/ A srvAudit core component is Advanced Shell History.
	<Y  https://code.google.com/p/advanced-shell-history
	/ \

License
-------

The project is licensed under the MIT license.

