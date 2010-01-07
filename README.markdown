setup_git_daemon
================

Generate a startup script to launch git daemon at boot.

### Linux ###
A daemon start file is generated in `/etc/service/git-daemon/run` that looks something like this:

	#!/bin/sh
	exec 2>&1
	echo "git daemon starting."
	exec chpst -uUSER GIT_PATH daemon --verbose --base-path=GIT_BASE_PATH


### Darwin ###
A Launchd start file is generated in `/Library/LaunchDaemons/org.git.daemon.plist` that looks something like this:

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