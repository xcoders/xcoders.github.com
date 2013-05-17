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
task :serve, [:port] do |t, args|
  sh "jekyll serve #{args.port || 4000} --watch --drafts"
end

desc "Publish site."
task :publish => [ :build ] do |t|
  # sh "rsync -avz --delete #{SITE_DIR}/ #{PUBLISH_HOST}:#{PUBLISH_PATH}"
  puts "Commit your posts and changes.\nThen run:\n  git push origin master"
end

desc "Create a new draft post"
task :draft, [:title, :author] do |t, args|
  # Get all drafts
  drafts = Dir.glob(File.join(DRAFTS_DIR, 'draft-*.*'))
  draft_id = 1
  if drafts.length() > 0
    draft_id = File.basename(drafts.sort()[-1]).gsub(/draft-/, '').gsub('.markdown', '').to_i() + 1
  end
  
  # Create a new file with a basic template
  postname = "draft-#{draft_id}"  
  FileUtils.mkdir_p DRAFTS_DIR unless File.exist?(DRAFTS_DIR)
  post = File.join(DRAFTS_DIR, "#{postname}.markdown")
  
  # Set the post title if not provided
  if args.title.nil?
    default_title = "Draft Post #{draft_id}"
    print "Enter post title [#{default_title}]: "
    title = $stdin.gets().chomp()
    title = default_title unless title.length() > 0
  else
    title = args.title
  end
  
  # Set author
  author = args.author || `git config --get user.name`.strip

  header = <<-END
---
layout: post
title: #{title}
author: #{author}
---

New draft post

END

  File.open(post, 'w') {|f| f << header }  
  edit_post(post)

  puts "Created draft post #{postname}"
  puts "To publish, use:"
  puts "  rake post [#{postname}]"
end

desc "Publish draft post. Arguments: [name]"
task :post, [:name] do |t, args|
  if args.name
    draft_name = File.extname(args.name).length() > 0 ? args.name : "#{args.name}.markdown"
    draft_path = File.join(DRAFTS_DIR, draft_name)
  else
    # Get list of drafts and prompt user to pick
    drafts = Dir.glob(File.join(DRAFTS_DIR, '*'))
    
    if drafts.count > 1
      menu = []
      puts "Select draft to post:"
      count = 1
      drafts.each do |draft|
        header, body = parse_post(IO.readlines(draft))
        menu << "#{count}. #{header['title']} [#{File.basename(draft)}]"
        count += 1
      end
      
      begin
        menu.each { |m| puts m }
        print "Which post? (1-#{drafts.length()} or q) "
        choice = $stdin.gets().chomp()
        if choice == 'q'
          puts "Abort"
          exit(-1)
        end
        choice = choice.to_i() - 1
      end while (choice < 1 or choice > drafts.length())
      draft_path = drafts[choice]
    elsif drafts.count == 1
      puts "Posting pending draft"
      draft_path = drafts[0]
    else
      puts "No pending drafts"
      exit(-1)
    end
  end
  
  unless File.exist?(draft_path)
    puts "Draft file #{draft_path} is missing"
    exit(-1)
  end
  
  # Generate timestamp
  published_timestamp = Time.now
  date_prefix = published_timestamp.strftime("%Y-%m-%d")
  header, body = parse_post(IO.readlines(draft_path))

  # Create the post file
  name = header["title"].strip.downcase.gsub(/ /, '-')
  header["date"] = published_timestamp.strftime("%Y-%m-%d %H:%M:%S") unless header.include?("date")
  post_path = File.join(POSTS_DIR, "#{date_prefix}-#{name}.markdown")
  write_post(header, body, post_path)
  
  # Clear draft
  File.delete(draft_path)
 
  puts "Posted: #{File.basename(post_path)}"
end

desc "Update published post. Arguments: [title]"
task :update, [:name] do |t, args|
  unless args.name
    puts "Usage: rake post[name]"
    exit(-1)
  end

  updated_timestamp = Time.now
  postname = args.name.strip.downcase.gsub(/ /, '-')
  post_path = File.join(POSTS_DIR, "#{postname}.markdown")
  header, body = parse_post(IO.readlines(post_path))
  header["updated"] = updated_timestamp.strftime("%Y-%m-%d %H:%M:%S")
  write_post(header, body, post_path)
  edit_post(post_path)
  puts "Updated: #{post_path}"
end
