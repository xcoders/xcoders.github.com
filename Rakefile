require 'rake'
require 'fileutils'
require 'yaml'
require 'time'

ROOT_DIR = File.dirname(__FILE__)
SITE_DIR = File.join(ROOT_DIR, '_site')
DRAFTS_DIR = File.join(ROOT_DIR, '_drafts')
POSTS_DIR = File.join(ROOT_DIR, '_posts')

PUBLISH_HOST = "host"
PUBLISH_PATH = "/path/seattlexcoders.org/web/public"

def categories(tags)
  categories = "categories:\n"
  if tags
    tags.split(%r{[\/\s]}).each { |t| categories << "- #{t.strip}\n" }
  end
  categories
end

def parse_post(post)
  raise Exception.new("Invalid post file format") unless post[0] = "---"
  eoh = post[1, post.length].index("---\n") + 1
  header = YAML.load(post[1, eoh].join)
  body = post[eoh + 1, post.length].join
  return header, body
end

def write_post(header, body, file)
  File.open(file, "w") do |f|
    f << YAML::dump(header)
    f << "---\n"
    f << body
  end
end

def edit_post(post)
  editor = ENV['VISUAL'] || ENV['EDITOR']
  system "#{editor} #{post}"  
end

desc "Clear generated site."
task :clean do
  rm_rf Dir.glob(File.join(SITE_DIR, '*'))
end

desc "Generate site."
task :build do
  sh "jekyll --time \"#{Time.now}\""
end

desc "Run local jekyll server"
task :server, [:port] do |t, args|
  sh "jekyll --server #{args.port || 4000} --auto --time \"#{Time.now}\""
end

desc "Publish site."
task :publish => [ :build ] do |t|
  # sh "rsync -avz --delete #{SITE_DIR}/ #{PUBLISH_HOST}:#{PUBLISH_PATH}"
  puts "Commit your posts and changes.\nThen run:\n  git push origin master"
end

desc "Create a new draft post"
task :draft, [:title, :author] do |t, args|
  title = args.title || "Untitled"
  author = args.author || `git config --get user.name`.strip

  # Create a new file with a basic template
  postname = title.strip.downcase.gsub(/ /, '-')
  post = File.join(DRAFTS_DIR, "#{postname}.markdown")

  header = <<-END
---
layout: post
title: #{title}
author: #{author}
---

New draft post

END

  mkdir_p(DRAFTS_DIR)
  File.open(post, 'w') {|f| f << header }  
  edit_post(post)

  puts "Created draft post #{post}."
  puts "To publish, use:"
  puts "  rake post [#{postname}]"
end

desc "Publish draft post. Arguments: [title]"
task :post, [:title] do |t, args|
  unless args.title
    puts "Usage: rake post[\"Title\"]"
    exit(-1)
  end

  published_timestamp = Time.now
  date_prefix = published_timestamp.strftime("%Y-%m-%d")
  draft_path = File.join(DRAFTS_DIR, "#{args.title}.markdown")
  header, body = parse_post(IO.readlines(draft_path))

  # Create the post file
  name = header["title"].strip.downcase.gsub(/ /, '-')
  header["date"] = published_timestamp.strftime("%Y-%m-%d %H:%M:%S") unless header.include?("date")
  post_path = File.join(POSTS_DIR, "#{date_prefix}-#{name}.markdown")
  write_post(header, body, post_path)
  
  # Clear draft
  File.delete(draft_path)
 
  puts "Posted: #{post_path}"
end

desc "Update published post. Arguments: [title]"
task :update, [:title] do |t, args|
  unless args.title
    puts "Usage: rake post[\"Title\"]"
    exit(-1)
  end

  updated_timestamp = Time.now
  postname = args.title.strip.downcase.gsub(/ /, '-')
  post_path = File.join(POSTS_DIR, "#{postname}.markdown")
  header, body = parse_post(IO.readlines(post_path))
  header["updated"] = updated_timestamp.strftime("%Y-%m-%d %H:%M:%S")
  write_post(header, body, post_path)
  edit_post(post_path)
  puts "Updated: #{post_path}"
end
