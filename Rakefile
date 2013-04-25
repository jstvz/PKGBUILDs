require 'rake'

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
