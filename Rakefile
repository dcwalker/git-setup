require 'rubygems'
require 'rake'


desc "Generate a startup script to launch git daemon at boot."
task :setup_git_daemon do
  GIT_BASE_PATH = ENV['GIT_BASE_PATH'] || ENV['HOME']

  os = %x[uname].chomp
  if os == "Linux"
    GIT_PATH = ENV['GIT_PATH'] || "/usr/bin/git"
    open("/etc/service/git-daemon/run", "w") do |file|
      file.puts <<-EOF
#!/bin/sh
exec 2>&1
echo "git daemon starting."
exec chpst -u#{ENV['USER']} #{GIT_PATH} daemon --verbose --base-path=#{GIT_BASE_PATH}
EOF

    end
  elsif os == "Darwin"
    GIT_PATH = ENV['GIT_PATH'] || "/usr/local/git/bin/git"
    open("/Library/LaunchDaemons/org.git.daemon.plist", "w") do |file|
      file.puts <<-EOF
<?xml version=\"1.0\" encoding=\"UTF-8\"?>
<!DOCTYPE plist PUBLIC \"-//Apple//DTD PLIST 1.0//EN\"
 \"http://www.apple.com/DTDs/PropertyList-1.0.dtd\">
<plist version=\"1.0\">
<dict>
	<key>Label</key>
	<string>org.git.daemon</string>
	<key>ProgramArguments</key>
	<array>
		<string>#{GIT_PATH}</string>
		<string>daemon</string>
		<string>--base-path=#{GIT_BASE_PATH}</string>
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
EOF

    end
  else
    puts "Don't know how to setup in #{os}"
  end
end

desc "Setup git remotes for collaboration." 
task :add_remotes do
    open("examplerc") do |file|
      file.each do |line|
        next if line =~ /^#/
        name, url = line.split(":")
        puts %x[git remote add #{name} #{url}]
      end
    end
end

# TODO
#desc "Add Plugins to app based on subversion externals.  Requires git-rails-plugins." 
task :setup_git_plugins do
end