#require 'pry'

# To sync Xcode settings into working dir:
# rsync -vcr --exclude=IDE* ~/Library/Developer/Xcode/UserData/ .

DIRS = ['CodeSnippets', 'FontAndColorThemes', 'KeyBindings']
FILES_IN_DIRS = []
DEST = File.expand_path('~/Library/Developer/Xcode/UserData')

namespace :symlink do
  desc "Symlinks dirs #{DIRS.join(",")} and all files in #{FILES_IN_DIRS.join(",")} into editor preferences folder"
  task :all => DIRS + FILES_IN_DIRS

  DIRS.each do |dir_to_symlink|
    desc "Symlinks #{dir_to_symlink} into #{DEST}"
    task dir_to_symlink.to_sym do
      safe_symlink2(dir_to_symlink)
    end
  end

  FILES_IN_DIRS.each do |dir_with_files|
    desc "Symlinks all files in #{dir_with_files} into #{File.join(DEST, dir_with_files)}"
    task dir_with_files.to_sym do
      Dir.foreach(File.join(File.expand_path('.'), dir_with_files)) do |file|
        next if file == '.' or file == '..'
        safe_symlink2(File.join(dir_with_files, file))
      end
    end
  end
end

namespace :reset do
  desc "Resets dirs #{DIRS.join(",")} and all files in #{FILES_IN_DIRS.join(",")}"
  task :all => DIRS + FILES_IN_DIRS

  DIRS.each do |dir_to_reset|
    desc "Resets symlinked dir #{File.join(DEST, dir_to_reset)}"
    task dir_to_reset.to_sym do
      reset_from_bak(File.join(DEST, dir_to_reset))
    end
  end

  FILES_IN_DIRS.each do |dir_with_files|
    desc "Resets all symlinked files in #{File.join(DEST, dir_with_files)}"
    task dir_with_files.to_sym do
      Dir.foreach(File.join(File.expand_path('.'), dir_with_files)) do |file|
        next if file == '.' or file == '..'
        reset_from_bak(File.join(DEST, dir_with_files, file))
      end
    end
  end
end

desc "Symlinks dirs #{DIRS.join(",")} and all files in #{FILES_IN_DIRS.join(",")} into editor preferences folder"
task :symlink_all do
  cwd = File.expand_path('.')
  DIRS.each do |dir|
    safe_symlink(File.join(cwd, dir), File.join(DEST, dir))
  end

  FILES_IN_DIRS.each do |dir|
    Dir.foreach(File.join(cwd, dir)) do |file|
      next if file == '.' or file == '..'
      safe_symlink(File.join(cwd, dir, file), File.join(DEST, dir, file))
    end
  end
end

task :reset do
  cwd = File.expand_path('.')
  DIRS.each do |dir|
    reset_from_bak(File.join(DEST, dir))
  end

  FILES_IN_DIRS.each do |dir|
    Dir.foreach(File.join(cwd, dir)) do |file|
      next if file == '.' or file == '..'
      reset_from_bak(File.join(DEST, dir, file))
    end
  end
end

def safe_symlink2(file)
  safe_symlink(File.join(File.expand_path('.'), file), File.join(DEST, file))
end

def safe_symlink(from, to)
  run("rm #{to}") if File.symlink?(to)
  run("mv #{to} #{to}.bak") if File.exist?(to)
  run("ln -s #{from} #{to}")
end

def reset_from_bak(file)
  run("rm #{file} && mv #{file}.bak #{file}") if(File.symlink?(file) && File.exist?("#{file}.bak"))
end

def run(cmd)
  puts "Running :#{cmd}\n\n"
  `#{cmd}`
end

