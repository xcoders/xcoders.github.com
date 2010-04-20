# Seattle Xcoders Web Site

This is the source for the [Seattle Xcoders](http://seattlexcoders.org) web site. It is built using Jekyll with a few modifications.

# Install:

* Install recent version of git
* Register for github account and give me your id (I think I have added both of you as collaborators to the project)
* Project is owned by xcoders user account on GitHub with all three of us as collaborators (meaning we can all push code to it)
* Project is http://github.com/xcoders/xcoders.github.com
* Check out source: git clone git@github.com:xcoders/xcoders.github.com.git
* Install jekyll (http://github.com/mojombo/jekyll) and dependencies so you can test site locally:

	sudo gem install jekyll RedCloth liquid maruku classifier directory_watcher open4
	sudo easy_install Pygments (for syntax highlighting in posts)


# Create a post
* rake draft[<post_title>, <author>]
  by default, post should use your GitHub name so you can omit the author field if you want
  Your default editor should open up the post file
  Posts by default are in markdown. If you prefer HTML, change the name to .html. If you prefer Textile, use .textile. I can add rake options to change the preferred format if you wish
* Edit the post file in any editor you want. I use TextMate which has nice syntax enhancements for Markdown and Textile. See _posts/* for examples of posts.
* rake post[<post_title>]
  Moves from the _drafts folder to _posts folder. Think of this as staging your post. You can now see it when you run the local jekyll server.

# Local server
* Run rake server to launch a local webrick with the site. Navigate to http://localhost:3000 to view the site. You can edit the post file all you want and see the changes automatically.

# Publish post live:
* When you are ready to publish the post live:

	git add _posts/<postfile>
	git commit
	git push origin master

* The git commit is to your local git repo.
* The git push to origin pushes the code to the Xcoders github repo. GitHub pages will be called as a post commit hook and regenerate the site.
* Your post is live

The above pattern also applies to CSS and any other pages (like about). So you can edit the site structure in the same way.

# Notes

The site uses Jekyll to build the pages. Jekyll is kind of a throw back. It is built in ruby and generates static pages. It uses Liquid as a templating engine. See the jekyll project wiki for lots of info using Liquid.

The site structure is as follows:

	CNAME            _drafts/         _site/           calendar.html
	README.markdown  _includes/       about.textile    css/
	Rakefile         _layouts/        archive.html     images/
	_config.yml      _posts/          atom.xml         index.html

<table>
	<tr>
		<td>CNAME</td>
		<td>File to allow github pages to serve seattlecoders.org</td>
	</tr>
	<tr>
		<td>Rakefile</td>
		<td>I wrote this with convenience tasks for dealing with the site/jekyll
	_config.yml Inputs to the jekyll engine for site generation</td>
	</tr>
	<tr>
		<td>_drafts</td>
		<td>Ignored by the site builder. You can build up posts here before brining them live.</td>
	</tr>
	<tr>
		<td>_includes</td>
		<td>Partials that can be included by other pages</td>
	</tr>
	<tr>
		<td>_layouts</td>
		<td>Page layouts (posts use the posts.html layout for example)</td>
	</tr>
	<tr>
		<td>_posts</td>
		<td>Blog posts. Each posts is a separate file</td>
	</tr>
	<tr>
		<td>_site</td>
		<td>Generated site (this is what is actually served)</td>
	</tr>
	<tr>
		<td>atom.xml</td>
		<td>RSS feed template</td>
	</tr>
	<tr>
		<td>about</td><td />
	</tr>
	<tr>
		<td>archive</td><td />
	</tr>
	<tr>
		<td>calendar</td><td />
	</tr>
	<tr>
		<td>index</td>
		<td>These 4 are like posts but are served as top level pages</td>
	</tr>
	<tr>
		<td>css/</td>
	    <td>Stylesheets</td>
	</tr>
	<tr>
		<td>images/</td>
		<td>Image resources</td>
	</tr>
</table>

**DON'T EDIT STUFF IN _site/**

If you change something there it will be overwritten the next time the site is regenerated. It's the "build" directory.

Generally, to post, you just do the following:

	rake draft
	edit
	rake post
	rake server to preview
	git commit
	git push -> go live
