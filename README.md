Shinmun - a git-based blog engine
=================================

Shinmun is a small git-based blog engine. Write posts in your favorite
editor, git-push it and serve your blog straight from a repository.

### Features

* Posts are text files formatted with [Markdown][8], [Textile][9] or [HTML][10]
* Runs on [Rack][6], [Kontrol][3] and [GitStore][7]
* Deploy via [git-push][11]
* Index, category and archive listings
* RSS feeds
* Syntax highlighting provided by [CodeRay][4]
* Markdown comments


### Quickstart

Install the gems:

    $ gem sources -a http://gems.github.com
    $ gem install rack BlueCloth rubypants coderay georgi-git_store georgi-kontrol georgi-shinmun

Create a sample blog:

    $ shinmun init myblog

This will create a directory with all necessary files. Now start the
web server:

    $ cd myblog
    $ rackup

Browse to the following url:

    http://localhost:9292

Voilà, your first blog is up and running!


### Writing Posts

Posts can be created by using the `shinmun` command inside your blog
folder:

    shinmun post 'The title of the post'

Shinmun will then create a post file in the right place, for example
in `posts/2008/9/the-title-of-the-post.md` and open it with $EDITOR.


### Post Format

Each blog post is just a text file with a YAML header and a body. The
YAML header is surrounded with 2 lines of 3 dashes. This format is
compatible with [Jekyll][13] and [Github Pages][14].

The YAML header has following attributes:

* `title`: mandatory
* `date`: posts need one, pages not
* `category`: a post belongs to one category
* `tags`: a comma separated list of tags

Example post:

    --- 
    date: 2008-09-05
    category: Ruby
    tags: bluecloth, markdown
    title: BlueCloth, a Markdown library
    ---
    This is the summary, which is by definition the first paragraph of the
    article. The summary shows up in category listings or the index listing.


### Syntax highlighting

Thanks to the fantastic highlighting library [CodeRay][4], highlighted
code blocks can be embedded easily in Markdown. For Textile support
you have to require `coderay/for_redcloth`. These languages are
supported: C, Diff, Javascript, Scheme, CSS, HTML, XML, Java, JSON,
RHTML, YAML, Delphi

To activate CodeRay for a code block, you have to declare the language
in lower case:

    @@ruby

    def method_missing(id, *args, &block)
      puts "#{id} was called with #{args.inspect}"
    end             

**Note that the declaration MUST be followed by a blank line!**


### Directory layout

    + assets
      + print.css
      + styles.css
    + config.ru
    + pages
      + about.md
    + posts
      + 2007
      + 2008
        + 9
          + my-article.md
    + templates
      + 404.rhtml
      + archive.rhtml
      + category.rhtml
      + category.rxml
      + _comments.rhtml
      + _comment_form.rhtml
      + index.rhtml
      + index.rxml
      + index.rhtml
      + layout.rhtml
      + page.rhtml
      + post.rhtml  

### Blog configuation

In `config.ru` you can set the properties of your blog:

    blog.config = {
      :language => 'en',
      :title => "Blog Title",
      :author => "The Author",
      :categories => ["Ruby", "Javascript"],
      :description => "Blog description"
    }


### Templates

Layout and templates are rendered by *ERB*.  The layout is defined in
`templates/layout.rhtml`. The content will be provided in the variable
`@content`. A minimal example:

    <html>
      <head>
        <title><%= @blog.title %></title>
        <%= stylesheet_link_tag 'style' %>
      </head>
      <body>
         <%= @content %>
      </body>
     </html>

The attributes of a post are accessible via the @post variable:

    <div class="article">
     
      <h1><%= @post.title %></h1>
     
      <div class="date">
        <%= human_date @post.date %>
      </div>
     
      <%= @post.body_html %>

      ...      

    </div>


### Commenting System

Comments are stored as flat files and encoded as YAML objects. Each
post has a corresponding comment file located at `comments/<path to
post>`. So administration of comments is possible by editing the YAML
file, which can be done on your local machine, as you can just pull
the comments from your live server.


### Deployment

Shinmun can server the blog straight from the git repository. So on
your webserver initialize a new git repo like:

    $ cd /var/www
    $ mkdir myblog
    $ cd myblog
    $ git init

Now on your local machine, you add a new remote repository and push
your blog to your server:

    $ cd ~/myblog
    $ git remote add live ssh://myserver.com/var/www/myblog
    $ git push live


On your production server, you just need the rackup file `config.ru`
to run the blog:

    $ git checkout config.ru

Now you can run just a pure ruby server or something like Phusion
Passenger. Anytime you want to publish a post on your blog, you
just write, commit and finally push a post by:

    $ git commit -a -m 'new post'
    $ git push live


### Phusion Passenger

Shinmun is compatible with [Phusion Passenger][5]. Install Phusion
Passenger as described in my [blog post][2].

Assuming that you are on a Debian or Ubuntu system, you can create a
file named `/etc/apache2/sites-available/blog`:

    <VirtualHost *:80>
        ServerName myblog.com
        DocumentRoot /var/www/blog/public
    </VirtualHost>

Enable the new virtual host:

    $ a2ensite myapp

After restarting Apache your blog should run on Apache on your desired
domain:

    $ /etc/init.d/apache2 restart


### GitHub Project

Download or fork the package at my [github repository][1]


[1]: http://github.com/georgi/shinmun
[2]: http://www.matthias-georgi.de/2008/9/quick-guide-for-passenger-on-ubuntu-hardy.html
[3]: http://github.com/georgi/kontrol
[4]: http://coderay.rubychan.de/
[5]: http://www.modrails.com/
[6]: http://github.com/rack/rack
[7]: http://github.com/georgi/git_store
[8]: http://daringfireball.net/projects/markdown/
[9]: http://textile.thresholdstate.com/
[10]: http://en.wikipedia.org/wiki/Html
[11]: http://www.kernel.org/pub/software/scm/git/docs/git-push.html
