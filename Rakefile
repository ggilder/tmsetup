require 'tmpdir'
require 'fileutils'

tmsupport_path = File.join(ENV['HOME'], 'Library', 'Application Support', 'TextMate')
tmplugins_path = File.join(tmsupport_path, 'PlugIns')
tmbundles_path = File.join(tmsupport_path, 'Bundles')

plugin_repos = [
  'https://github.com/enormego/EGOTextMateFullScreen.git',
  'https://github.com/fdintino/projectplus.git'
]

bundles_repo = 'git@github.com:ggilder/tmsetup.git'

desc "Install latest versions of TextMate plugins"
task :plugins do |t|
  Dir.mktmpdir do |build_dir|
    puts "Installing TextMate Plugins..."
    puts "tmp dir #{build_dir}"
    plugin_repos.each do |repo|
      repo_dir = File.basename(repo, File.extname(repo))
      puts "Building #{repo_dir}..."
      `cd #{build_dir} && git clone #{repo} && cd #{repo_dir} && xcodebuild`
    end
    products = Dir.glob(File.join(build_dir, '*', 'build', 'Release', '*.tmplugin'))
    products.each do |product|
      print "Installing #{File.basename(product)}... "
      destination = File.join(tmplugins_path, File.basename(product))
      remove_entry_secure(destination, true) if File.exists?(destination)
      FileUtils.mv product, tmplugins_path
      puts "installed."
    end
  end
end

desc "Install or update TextMate bundles"
task :bundles do |t|
  puts "Updating TextMate Bundles at #{tmbundles_path}..."
  git_branch = `cd "#{tmbundles_path}" && git symbolic-ref HEAD 2>/dev/null`
  if git_branch.empty?
    puts "No git repo in bundle path, setting up tracking..."
    `cd "#{tmbundles_path}" && git init && git remote add origin #{bundles_repo} && git fetch origin && git branch -t bundles origin/bundles && git checkout -f bundles && git submodule init && git submodule update`
    puts "Done setting up."
  else
    `cd "#{tmbundles_path}" && git pull && git submodule init && git submodule update`
    puts "Done updating."
  end
  
  # reload bundles
  `osascript -e 'tell app "TextMate" to reload bundles'` if `ps -x` =~ /TextMate\.app/
end

desc "Install TextMate plugins and bundles"
task :tmsetup => [:bundles, :plugins]