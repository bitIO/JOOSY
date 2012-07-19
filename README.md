JOOSY - JOOmla SYnch
==================
A command line tool to perform synchronization between different joomla environments. Typically
sandbox, development, stage, producction, ...

Author  : Francisco Calle Moreno (http://www.fcallem.net)  
Version : 0.0.2 (2012-07-17)   

Copyright 2012, Francisco Calle Moreno   

Apache Licensed (http://www.apache.org/licenses/LICENSE-2.0)

#Disclaimer
This is a tool I've created to help myself while developing joomla sites. It's not intended to be THE TOOL you need but a starting point. For sure it needs improvement, so that, if you are willing to help, you're very welcome. If you don't like, that's fine with me, but please, don't let me know such a thing.

#Requirements

* Linux / Mac OSX / Cywyng? (not tested)
* rsync, tar, gzip
* mysql (commnad line client)
* … and some faith ;-)

#What does JOOSY do?
** Mainly, it synchronizes two environments (files and database). The files are synched using rsycn. Database, in the other hand, it's backed up and then replaced. I'm working to find a better and more elegant way but ... **

#How to use JOOSY
Firt thing you need to do is to define, at least, two different environments. You do that by adding them to the **sites.conf** file. Here is an example:

	#
	# LOCAL ENVIRONMENT
	#
	dev-env:uri:dev.mysite.com
	dev-env:host:localhost
	dev-env:host-tmp:/tmp
	dev-env:root:/home/my-local-user/Sites/joomla/mysite/dev/
	dev-env:rsync:-rzlt --delete --force --exclude=configuration.php
	dev-env:mysql-host:localhost
	dev-env:mysql-db:joomla
	dev-env:mysql-user:joomla
	dev-env:mysql-password:joomla
	dev-env:mysql-prefix:mysitedev_
	dev-env:mysql-skip:users;usergroups;user_usergroup_map;user_profiles;user_notes;session;
	
	#
	# REMOTE ENVIRONMENTS
	#
	prod-env:uri:mysite.com
	prod-env:host:remote.host.com
	prod-env:host-user:myremoteuser
	prod-env:host-tmp:/home/myremoteuser/tmp
	prod-env:root:/home/myremoteuser/www/joomla/mysite/
	prod-env:rsync:-ruzl --exclude=configuration.php
	prod-env:mysql-host:mysql.cms.mysite.com
	prod-env:mysql-db:joomla
	prod-env:mysql-user:joomjoomla
	prod-env:mysql-password:joomla
	prod-env:mysql-prefix:mysiteprod_
	prod-env:mysql-skip:users;usergroups;user_usergroup_map;user_profiles;user_notes;session;

Every option is self-explained by its name. Just a few remmarks:

* **root** value should be ended with a **trailing slash**
* **rsync flags** is applied when the environment **is the target**, not the source.
* I'd recommed not to sync configuration.php and that's why I've added the --exclude option
* mysql-skip table list **ALWAYS** has to end with ;

Once your done with the configuration file, you have to specify its location in the joosy script (line 27)

And here is the command line

	joosy -s <source name> -t <target name>  -m <mode name>
	    -s : source distribution name (specified in conf file) 
	    -t : target distribution name (specified in conf file) 
	    -r : specify the rsync flags to be used (for files and database) 
			* clone 
			* update (default mode)
			* updateandclean
		-b : make backups during the process (default value is yes)
			
-m parameter is optional and applies to how rsync of files is going to be executed. Let's take a look of what means each name:

* clone: flags used are `-zrlt --delete --exclude=configuration.php` and is meant to create a replica of the source. By now, it does not delete previously the content of the target but during the rsync process. Should it be done?
* update: **this is the default mode**. Flags used are `-zurlt --exclude=configuration.php`. This mode is meant to be used once you are, more o less, in sync with the target
* updateanddelete: flags are `-zurlt --delete --exclude=configuration.php`. For those days when you want to be clean up your house XD (this mode may be removed in future versions)

Here is an example for an update

	joosy -s dev-env -t prod-env
	
and here how you would clone for the first time

	joosy -s dev-env -t prod-env -m clone	

#KNOWN PROBLEMS / ISSUES

* `rsync: failed to set times on` This can occur when you don't own the directory. If this is happening to you, you should remove the -t flag. But **keep in mind** that by doing this, there will be a time penalty (ref [http://www.errorhelp.com/search/details/73579/rsync-failed-to-set-times-on-operation-not-permitted](http://))



#TODOs
(In order of what I consider important)

* Allow sync datbase tables, not the hole db
* Generate and apply patchs to the configuration file to avoid the --exclude flag
* …

<strong style="color:red">Comments, ideas, suggestions, improvements and HELP are very very welcome</strong>