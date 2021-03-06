setup_git_daemon
================

Generate a startup script to launch git daemon at boot.  Set `GIT_PATH` to git's location on your machine and `GIT_BASE_PATH` to the location you'd like to serve git projects from (think Apache document root).  Once git-daemon is running you just need to allow git-daemon to share your projects.  For each project you want to share `touch` a `git-daemon-export-ok` file inside the project's `.git` directory (ex: `cd $GIT_BASE_PATH/git-setup; touch .git/git-daemon-export-ok;`).

### Linux ###
A daemon start file is generated in `/etc/service/git-daemon/run` that looks something like this. GIT path is assumed to be `/usr/bin/git` unless otherwise defined.

	#!/bin/sh
	exec 2>&1
	echo "git daemon starting."
	exec chpst -uUSER GIT_PATH daemon --verbose --base-path=GIT_BASE_PATH


### Darwin ###
A Launchd start file is generated in `/Library/LaunchDaemons/org.git.daemon.plist` that looks something like this. GIT path is assumed to be `/usr/local/git/bin/git` unless otherwise defined.

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
	 "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
	<plist version=\"1.0\">
	<dict>
		<key>Label</key>
		<string>org.git.daemon</string>
		<key>ProgramArguments</key>
		<array>
			<string>GIT_PATH</string>
			<string>daemon</string>
			<string>--base-path=GIT_BASE_PATH</string>
		</array>
		<key>RunAtLoad</key>
		<true/>
		<key>OnDemand</key>
		<true/>
	  <key>Sockets</key>
	  <dict>
	      <key>Listeners</key>
	      <dict>
	          <key>SockServiceName</key>
	          <string>git</string>
	          <key>SockType</key>
	          <string>dgram</string>
	          <key>SockFamily</key>
	          <string>IPv4</string>
	        </dict>
	    </dict>
	  </dict>
	</plist>

Once the file exists you need to add it to the launchd system.  Start by loading `launchctl` and adding the git daemon to it's list.

  dcwalker:~ $ launchctl
  launchd% load /Library/LaunchDaemons/org.git.daemon.plist
  launchd% start org.git.daemon
  launchd% exit
  dcwalker:~ $
  
Now you can check that it's running with `ps aux | grep git`

add_remotes
===========

Setup git remotes for collaboration. Set `GIT_PROJECT_PATH` to the location of the specific git project to add remote repositories to.  Not setting the `GIT_PROJECT_PATH` causes remotes to be added to all the projects in `GIT_BASE_PATH`.  Remote data comes from the `project_remotes_input` file, check the `project_remotes_input.example` file syntax.  Comment lines in your input file with `#`.

The task assumes your project name (directory name) is the same as the remote's project name (ex: local version is `git-setup` so the remote version should be `git-setup`).
