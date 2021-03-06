#!/usr/bin/env ruby
require 'date'
require 'rake/clean'
require 'rake/testtask'
require 'rake/packagetask'
require 'rbconfig'

PKG_NAME = 'hornetseye-rmagick'
PKG_VERSION = '1.0.3'
RB_FILES = FileList[ 'lib/**/*.rb' ]
TC_FILES = FileList[ 'test/tc_*.rb' ]
TS_FILES = FileList[ 'test/ts_*.rb' ]
PKG_FILES = [ 'Rakefile', 'README.md', 'COPYING', '.document' ] +
            RB_FILES + TS_FILES + TC_FILES
BIN_FILES = [ 'README.md', 'COPYING', '.document' ] +
            RB_FILES + TS_FILES + TC_FILES
SUMMARY = %q{RMagick integration for Hornetseye}
DESCRIPTION = %q{This Ruby extension implements the necessary conversions to save and load images stored in Hornetseye::MultiArray.}
LICENSE = 'GPL-3+'
AUTHOR = %q{Jan Wedekind}
EMAIL = %q{jan@wedesoft.de}
HOMEPAGE = %q{http://wedesoft.github.com/hornetseye-rmagick/}

$SITELIBDIR = RbConfig::CONFIG[ 'sitelibdir' ]

task :default => :all

desc 'Do nothing (default)'
task :all do
end

desc 'Install Ruby extension'
task :install do
  verbose true do
    for f in RB_FILES do
      FileUtils.mkdir_p "#{$SITELIBDIR}/#{File.dirname f.gsub( /^lib\//, '' )}"
      FileUtils.cp_r f, "#{$SITELIBDIR}/#{f.gsub /^lib\//, ''}"
    end
  end
end

desc 'Uninstall Ruby extension'
task :uninstall do
  verbose true do
    for f in RB_FILES do
      FileUtils.rm_f "#{$SITELIBDIR}/#{f.gsub /^lib\//, ''}"
    end
  end
end

Rake::TestTask.new do |t|
  t.test_files = TC_FILES
end

begin
  # For development just run it with "yard server --reload".
  require 'yard'
  YARD::Rake::YardocTask.new :yard do |y|
    y.files << RB_FILES
  end
rescue LoadError
  STDERR.puts 'Please install \'yard\' if you want to generate documentation'
end

Rake::PackageTask.new PKG_NAME, PKG_VERSION do |p|
  p.need_tar = true
  p.package_files = PKG_FILES
end

begin
  require 'rubygems'
  require 'rubygems/builder'
  $SPEC = Gem::Specification.new do |s|
    s.name = PKG_NAME
    s.version = PKG_VERSION
    s.platform = Gem::Platform::RUBY
    s.date = Date.today.to_s
    s.summary = SUMMARY
    s.description = DESCRIPTION
    s.license = LICENSE
    s.author = AUTHOR
    s.email = EMAIL
    s.homepage = HOMEPAGE
    s.files = PKG_FILES
    s.test_files = TC_FILES
    s.require_paths = [ 'lib' ]
    s.rubyforge_project = %q{hornetseye}
    s.extensions = %w{Rakefile}
    s.has_rdoc = 'yard'
    s.extra_rdoc_files = []
    s.rdoc_options = %w{--no-private}
    s.add_dependency %q<rmagick>, [ '~> 2.0' ]
    s.add_dependency %q<malloc>, [ '~> 1.4' ]
    s.add_dependency %q<multiarray>, [ '~> 1.0' ]
    s.add_development_dependency %q{rake}
  end
  GEM_SOURCE = "#{PKG_NAME}-#{PKG_VERSION}.gem"
  $BINSPEC = Gem::Specification.new do |s|
    s.name = PKG_NAME
    s.version = PKG_VERSION
    s.platform = Gem::Platform::CURRENT
    s.date = Date.today.to_s
    s.summary = SUMMARY
    s.description = DESCRIPTION
    s.license = LICENSE
    s.author = AUTHOR
    s.email = EMAIL
    s.homepage = HOMEPAGE
    s.files = BIN_FILES
    s.test_files = TC_FILES
    s.require_paths = [ 'lib' ]
    s.rubyforge_project = %q{hornetseye}
    s.has_rdoc = 'yard'
    s.extra_rdoc_files = []
    s.rdoc_options = %w{--no-private}
    s.add_dependency %q<rmagick>, [ '~> 2.0' ]
    s.add_dependency %q<malloc>, [ '~> 1.4' ]
    s.add_dependency %q<multiarray>, [ '~> 1.0' ]
  end
  GEM_BINARY = "#{PKG_NAME}-#{PKG_VERSION}-#{$BINSPEC.platform}.gem"
  desc "Build the gem file #{GEM_SOURCE}"
  task :gem => [ "pkg/#{GEM_SOURCE}" ]
  file "pkg/#{GEM_SOURCE}" => [ 'pkg' ] + $SPEC.files do
    when_writing 'Creating GEM' do
      Gem::Builder.new( $SPEC ).build
      verbose true do
        FileUtils.mv GEM_SOURCE, "pkg/#{GEM_SOURCE}"
      end
    end
  end
  desc "Build the gem file #{GEM_BINARY}"
  task :gem_binary => [ "pkg/#{GEM_BINARY}" ]
  file "pkg/#{GEM_BINARY}" => [ 'pkg' ] + $BINSPEC.files do
    when_writing 'Creating binary GEM' do
      Gem::Builder.new( $BINSPEC ).build
      verbose true do
        FileUtils.mv GEM_BINARY, "pkg/#{GEM_BINARY}"
      end
    end
  end
rescue LoadError
  STDERR.puts 'Please install \'rubygems\' if you want to create Gem packages'
end

CLOBBER.include 'doc', '.yardoc'
