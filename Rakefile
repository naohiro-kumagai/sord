require "bundler/gem_tasks"
require "rspec/core/rake_task"

RSpec::Core::RakeTask.new(:spec)

task :default => :spec

REPOS = {
  discordrb: 'https://github.com/meew0/discordrb',
  rouge: 'https://github.com/rouge-ruby/rouge',
  yard: 'https://github.com/lsegal/yard',
  addressable: 'https://github.com/sporkmonger/addressable',
  zeitwerk: 'https://github.com/fxn/zeitwerk',
  'rspec-core': 'https://github.com/rspec/rspec-core',
  bundler: 'https://github.com/bundler/bundler',
  haml: 'https://github.com/haml/haml',
  gitlab: 'https://github.com/NARKOZ/gitlab'
}

namespace :examples do
  desc "Clone git repositories and run Sord on them as examples"
  task :seed do
    require 'fileutils'
    require 'colorize'

    if File.directory?('sord_examples')
      puts 'sord_examples directory already exists, please delete the directory before seeding, or run a reseed!'.red
      exit
    end

    FileUtils.mkdir 'sord_examples'
    FileUtils.cd 'sord_examples'

    # Shallow clone each of the repositories and then bundle install and run sord.
    REPOS.each do |name, url|
      puts "Cloning #{name}..."
      `git clone #{url} --depth=1`
      FileUtils.cd name.to_s
      # Add sord to gemfile.
      `echo "gem 'sord', path: '../../'" >> Gemfile`
      # Run bundle install.
      `bundle install`
      # Generate sri
      puts "Generating rbi for #{name}..."
      `bundle exec sord ../#{name}.rbi`
      puts "#{name}.rbi generated!"
      FileUtils.cd '..'
    end

    puts "Seeding complete!"
  end

  desc 'Regenerate the rbi files in sord_examples.'
  task :reseed do
    require 'fileutils'

    FileUtils.cd 'sord_examples'
    REPOS.keys.each do |name|
      FileUtils.cd name.to_s
      puts "Regenerating rbi file for #{name}..."
      `bundle exec sord ../#{name}.rbi --no-regenerate`
      FileUtils.cd '..'
    end
    
    puts "Re-seeding complete!"
  end
  
  desc 'Delete the sord_examples directory to allow the seeder to run again.'
  task :reset do
    require 'fileutils'
 
    FileUtils.rm_rf 'sord_examples' if File.directory?('sord_examples')
  end
end

