require 'rubygems'
require 'rake'

# Where are projects stored on system?  Used for setting the root for
# git-daemon and determining shared names
GIT_BASE_PATH = ENV['GIT_BASE_PATH'] || ENV['HOME']

desc "Generate a startup script to launch git daemon at boot."
task :setup_git_daemon do
  os = `uname`.chomp
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
  # GIT_PROJECT_PATH is the location of the project to add remotes to.
  # Not setting this causes remotes to be added to all projects in the
  # GIT_BASE_PATH
  GIT_PROJECT_PATH = ENV['GIT_PROJECT_PATH'] ? ENV['GIT_PROJECT_PATH'] : File.expand_path(File.join(GIT_BASE_PATH, '*'))
  
  (Dir.glob(GIT_PROJECT_PATH) - ['.', '..','.git','git-setup']).each do |project_path|
    next unless File.directory?(project_path)
    # Skip if the project_path isn't a git project
    next if `cd #{project_path}; git symbolic-ref HEAD 2> /dev/null` == ""
    # Assume the project name is the same as the directory name
    project_name = project_path.split("/").last
    puts "--#{project_name}--"
    open("project_remotes_input") do |file|
      file.each do |line|
        next if line =~ /^#/
        line = line.chomp
        puts "git remote add #{line}#{project_name}.git"
        `cd #{project_path}; git remote add #{line}#{project_name}.git`
      end
    end
  end
end