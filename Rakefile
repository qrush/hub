require 'rake/testtask'

task :default => :test

Rake::TestTask.new do |t|
  t.libs << 'lib'
  t.pattern = 'test/**/*_test.rb'
  t.verbose = false
end

desc "Launch Kicker (like autotest)"
task :kicker do
  puts "Kicking... (ctrl+c to cancel)"
  exec "kicker -e rake test bin"
end

desc "Build a gem"
task :gem => [ :gemspec, :build ]

desc "Build standalone script"
task :standalone => [ :test, :load_hub ] do
  Hub::Standalone.save('hub')
end

task :load_hub do
  $LOAD_PATH.unshift 'lib'
  require 'hub'
end

begin
  require 'jeweler'
  $LOAD_PATH.unshift 'lib'
  require 'hub'
  Jeweler::Tasks.new do |gemspec|
    gemspec.name = "hub"
    gemspec.summary = gemspec.description = "hub introduces git to GitHub"
    gemspec.homepage = "http://github.com/defunkt/hub"
    gemspec.version = Hub::Version
    gemspec.authors = ["Chris Wanstrath"]
    gemspec.email = "chris@ozmm.org"
    gemspec.executables = ["hub"]
    gemspec.post_install_message = <<-message

------------------------------------------------------------

                  You there! Wait, I say!
                  =======================

       If you are a heavy user of `git` on the command
       line  you  may  want  to  install `hub` the old
       fashioned way.  Faster  startup  time,  you see.

       Check  out  the  installation  instructions  at
       http://github.com/defunkt/hub#readme  under the
       "Standalone" section.

       Cheers,
       defunkt

------------------------------------------------------------

message
  end
rescue LoadError
  puts "Jeweler not available."
  puts "Install it with: gem install jeweler"
end

desc "Push a new version to Gemcutter"
task :publish => [ :test, :gemspec, :build ] do
  system "git tag v#{Hub::Version}"
  system "git push origin v#{Hub::Version}"
  system "git push origin master"
#   system "gem push pkg/hub-#{Hub::Version}.gem"
  system "git clean -fd"
  exec "rake pages"
end

desc "Publish to GitHub Pages"
task :pages => [ :check_dirty, :standalone ] do
  `git checkout gh-pages`
  `mv hub standalone`
  `git add standalone*`
  `git commit -m "update standalone"`
  `git push origin gh-pages`
  `git checkout master`
  puts :done
end

task :check_dirty do
  if !`git status`.include?('nothing to commit')
    abort "dirty index - not publishing!"
  end
end
