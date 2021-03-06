#!/usr/bin/env rake
require 'bundler/gem_tasks'
require 'rake/testtask'

namespace :twitter do

  desc "Pulls Twitter's Bootstrap Scss"
  task :pull do
    if !system "cd bootstrap && git pull"
      abort "...."
    end
  end

  BS_JS     = FileList["bootstrap/js/*.*"]
  ASSETS_JS = BS_JS.pathmap("app/assets/javascripts/twitter/bootstrap/%f")
  ASSETS_JS.zip(BS_JS).each do |target, source|
    file target => [source] { cp source, target, verbose: true }
  end

  BS_SCSS = FileList["bootstrap/scss/*.*"]
  ASSETS_SCSS = BS_SCSS.pathmap("app/assets/stylesheets/twitter/bootstrap/_%f")
  ASSETS_SCSS.zip(BS_SCSS).each do |target, source|
    target.gsub!(/__/, '_')
    file target => [source] { cp source, target, verbose: true }
  end

  desc "Update Twitter's Bootstrap JS"
  task :js => ASSETS_JS do
    js = {}
    ASSETS_JS.pathmap("%f").each { |f| js[f] = 1 }

    # dependencies
    order = %w{transition.js alert.js button.js carousel.js collapse.js dropdown.js modal.js tooltip.js popover.js scrollspy.js tab.js affix.js}
    order.each_with_index {|o, i| js[o] = i }

    list = js.to_a.sort {|a,b| a[1] <=> b[1]}.map{|p| "twitter/bootstrap/#{p[0]}"}
    list << "holder.js"
    File.write "app/assets/javascripts/twitter/bootstrap.js", list.map {|f| "//= require #{f}"}.join("\n")
  end

  desc "Update Twitter's Bootstrap SCSS"
  task :scss => ASSETS_SCSS do
    File.write "app/assets/stylesheets/twitter/bootstrap.scss", '@import "twitter/bootstrap/bootstrap";'
  end

  desc "Clean gem assets files"
  task :clean do
    FileUtils.rm_rf 'app/assets'
    FileUtils.mkpath 'app/assets/javascripts/twitter/bootstrap'
    FileUtils.mkpath 'app/assets/stylesheets/twitter/bootstrap'
  end

  desc "Update Twitter's Bootstrap Assets"
  task :assets => [:pull, :clean, :scss, :js]

end

Rake::TestTask.new do |t|
  t.libs << "test"
  t.test_files = FileList['test/*_test.rb']
  t.verbose = true
end
