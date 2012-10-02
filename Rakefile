require 'rake'
require 'rake/clean'

CLEAN.include '*/*.tar.*', '*/src', '*/pkg'

desc 'Run namcap on all PKGBUILDs.'
task :namcap do
  Dir.glob('*/PKGBUILD').each do |pkgbuild|
    puts "Package: #{pkgbuild.split('/').first}"
    IO.popen ['namcap', pkgbuild] do |pipe|
      pipe.each_line do |line|
        puts '=> ' + line
      end
    end
    puts ''
  end
end

task :default => :namcap
