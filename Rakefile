require 'tmpdir'
require 'fileutils'

tmplugins_path = File.join(ENV['HOME'], 'Library', 'Application Support', 'TextMate', 'PlugIns')

plugin_repos = [
  'https://github.com/enormego/EGOTextMateFullScreen.git',
  'https://github.com/fdintino/projectplus.git'
]

task :install_plugins do |t|
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