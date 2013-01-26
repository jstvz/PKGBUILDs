require 'rake'
require 'rake/clean'

require 'date'

# initialize clean task using .gitignore
File.readlines('.gitignore').each do |ignore_regex|
  unless ignore_regex.empty? or ignore_regex.start_with?('#')
    CLEAN.include("**/#{ignore_regex.strip}")
  end
end

def parse_pkgfile(names2pkgvars)
    pkgvars = {}
    File.open 'PKGBUILD' do |pkgbuild|
      pkgbuild_content = pkgbuild.read.lines
      names2pkgvars.each do |name, pkgvar|
        pkgbuild_content.each do |line|
          match = /^#{pkgvar}=(.+)/.match(line)
          pkgvars[name] = match[1] if match
        end
        unless pkgvars.has_key? name and pkgvars[name]
          raise "Could not determine the value of #{pkgvar}"
        end
      end
    end
    pkgvars
end

def prepare_gitrepo(gitroot, branch, repo)
  unless File.exists?(repo)
    puts "Cloning GIT Repository #{gitroot}..."
    puts %x(git clone #{gitroot} #{repo})
  end
  puts "Updating local GIT Repository #{repo}..."
  puts %x(cd #{repo} && git reset --hard && git checkout #{branch} && git pull)
end

# Checks if there were any commit since the given date.
# Date has to be in the format YYYYMMDD.
def commits_since?(date, repo)
  # convert date to git's format: YYYY-MM-DD
  gitdate = String.new(date)
  gitdate.insert(6, '-')
  gitdate.insert(4, '-')

  FileUtils.cd(repo) do
    return %x(git log --date=short --since=#{gitdate}).empty? == false
  end
end

def publish_package
  puts %x(makepkg --source)
  puts %x(aurploader *.src.tar.gz)
end

desc 'Check git packages for updates and publish them.'
task :git do
  Dir.glob('*-git').each do |pkg|
    FileUtils.cd(pkg) do
      puts "[Package #{pkg}]"
      repo = "src/#{pkg}"
      pkgvars = parse_pkgfile(:date => :pkgver, :gitroot => :_gitroot,
                                :branch => :_gitname)

      prepare_gitrepo(pkgvars[:gitroot], pkgvars[:branch], repo)

      if commits_since?(pkgvars[:date], repo)
        # update PKGBUILD and publish the new package
        today = Date.today.to_s.gsub(%r/-/, '')
        puts "Setting pkgver to #{today}..."
        newpkgbuild = File.read('PKGBUILD')
        newpkgbuild.sub!(%r/^pkgver=.*$/, "pkgver=#{today}")
        File.open('PKGBUILD', 'w') { |file| file.puts(newpkgbuild) }

        puts 'Publishing package...'
        publish_package
      else
        puts "No commit since #{pkgvars[:date]} (pkgver)"
      end
      puts ''
    end
  end
end

desc 'Run namcap on all PKGBUILDs.'
task :namcap do
  Dir.glob('*/PKGBUILD').each do |pkgbuild|
    puts "Package: #{pkgbuild.split('/').first}"
    IO.popen(['namcap', pkgbuild]) do |pipe|
      pipe.each_line { |line| puts '=> ' + line }
    end
    puts ''
  end
end

task :default => :namcap
