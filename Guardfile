# A sample Guardfile
# More info at https://github.com/guard/guard#readme

guard :bundler do
  watch('Gemfile')
end

guard :spork, :rspec_env => { 'RAILS_ENV' => 'test' }, test_unit: false do
  watch('config/application.rb')
  watch('config/environment.rb')
  watch('config/environments/test.rb')
  watch(%r{^config/initializers/.+\.rb$})
  watch('Gemfile.lock')
  watch('spec/spec_helper.rb') { :rspec }
end

guard :rspec, cmd: "bundle exec spring rspec --drb" do
  require "guard/rspec/dsl"
  dsl = Guard::RSpec::Dsl.new(self)

  # RSpec files
  rspec = dsl.rspec
  watch(rspec.spec_helper) { rspec.spec_dir }
  watch(rspec.spec_support) { rspec.spec_dir }
  watch(rspec.spec_files)

  # Ruby files
  ruby = dsl.ruby
  dsl.watch_spec_files_for(ruby.lib_files)

  # Factories
  watch(/^spec\/factories\/(.+)\.rb$/) do |m|
    puts "caught change to factories #{m}"
    rspec.spec_dir
  end

  # Rails files
  rails = dsl.rails(view_extensions: %w(erb haml slim))
  rails.workers = /^app\/workers\/(.+)_controller\.rb$/
  dsl.watch_spec_files_for(rails.app_files)

  watch(rails.workers) do |m|
    puts "caught change to controller #{m}"
    rspec.spec.("workers/#{m[1]}")
  end

  watch(rails.controllers) do |m|
    puts "caught change to controller #{m}"
    stripped = m[1].gsub('v1/', '')
    [
      rspec.spec.("routing/#{stripped}_routing"),
      rspec.spec.("controllers/#{stripped}_controller"),
      rspec.spec.("acceptance/#{stripped}"),
      rspec.spec.("requests/#{stripped}")
    ]
  end

  watch(rails.models) do |m|
    puts "caught change to model #{m}"
    [
      rspec.spec.("models/#{m[1]}")
    ]
  end

  # Rails config changes
  watch(rails.spec_helper)     { rspec.spec_dir }
  watch(rails.routes)          { "#{rspec.spec_dir}/routing" }
  watch(rails.app_controller)  { "#{rspec.spec_dir}/controllers" }
end
