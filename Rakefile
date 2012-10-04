require 'rake'
require 'rake/clean'

File.readlines('.gitignore').each do |ignore_regex|
  unless ignore_regex.empty? or ignore_regex.start_with? '#'
    CLEAN.include '**/' + ignore_regex.strip
  end
end

desc 'Run namcap on all PKGBUILDs.'
task :namcap do
  Dir.glob('*/PKGBUILD').each do |pkgbuild|
    puts "Package: #{pkgbuild.split('/').first}"
    IO.popen ['namcap', pkgbuild] do |pipe|
      pipe.each_line { |line| puts '=> ' + line }
    end
    puts ''
  end
end

task :default => :namcap
