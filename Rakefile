require 'rubygems'
require 'xcodebuild'
require 'cucumber/rake/task'

HERE = File.expand_path( '..',__FILE__ )
ENV['APP_BUNDLE_PATH'] = File.join( HERE, 'ci_artifacts/Frankified.app' )

namespace :xcode do
  XcodeBuild::Tasks::BuildTask.new :debug_simulator do |t|
    t.invoke_from_within = "2012Olympics"
    t.configuration = "Debug"
    t.sdk = "iphonesimulator"
    t.formatter = XcodeBuild::Formatters::ProgressFormatter.new
  end
end

task :default => ["xcode:debug_simulator:cleanbuild"]

namespace :ci do
  def move_into_artifacts( src )
    FileUtils.mkdir_p( 'ci_artifacts' )
    FileUtils.mv( src, "ci_artifacts/" )
  end

  task :clear_artifacts do
    FileUtils.rm_rf( 'ci_artifacts' )
  end

  task :build => ["xcode:debug_simulator:cleanbuild"] do
    move_into_artifacts( "2012Olympics/build/Debug-iphonesimulator/2012Olympics.app" )
  end
  
  task :frank_build do
    sh '(cd "2012Olympics" && frank build)'
    move_into_artifacts( "2012Olympics/Frank/frankified_build/Frankified.app" )
  end
  
  Cucumber::Rake::Task.new(:frank_test, 'Run Frank acceptance tests, generating HTML report as a CI artifact') do |t|
    t.cucumber_opts = "2012Olympics/Frank/features --format pretty --format html --out ci_artifacts/frank_results.html"
  end
end

task :ci => ["ci:clear_artifacts","ci:build","ci:frank_build","ci:frank_test"]

task :build => ["ci:clear_artifacts","ci:build","ci:frank_build"]

task :test => ["ci:frank_test"]