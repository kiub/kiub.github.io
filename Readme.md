# Bloggin with Octopress 3.0

* [Installation](#installation)
* [Gemfile](#gemfile)
* [Octopress commands](#octopress-commands)
    - [Publish a draft](#publish-a-draft)
    - [Unpublish a post](#unpublish-a-post)
    - [Isolate](#isolate)
    - [Deploy](#deploy)
        + [Master and Source branches](#master-and-source-branches)
* [Templates](#templates)
* [Embed GitHub Gists in your Jekyll or Octopress blog](#embed-github-gists-in-your-jekyll-or-octopress-blog)

## Installation

Before make shure that Ruby is installed
```sh
$ ruby -v
ruby 2.1.2p95 (2014-05-08 revision 45877) [x86_64-darwin14.0]
```

Ok, lets install Octopress
```sh
gem install octopress --pre
gem install jekyll
```

Create a NEW octopress project
```sh
$ octopress new tech-blog
```

```sh
$ cd blog
$ octopress serve -w
```
We can use `jekyll s` or `jekyll serve` as well, Octopress is build on top of Jekyll, note that `-w` in the `octopress` command will watch for any changes and apply it directly to the blog, without this key you’ll need to restart octopress everytime you make a change.

## Gemfile
```ruby
source 'https://rubygems.org'

gem 'jekyll'

group :jekyll_plugins do
    gem 'octopress', '~> 3.0.0.rc'
end
```

If we need a new gem (now themes ang plugins are managed as gems), include it in Gemfile and install

```sh
bundle install
```

## Octopress commands

Here are the subcommands for Octopress.

```ruby
init <PATH>         # Adds Octopress scaffolding to your site
new <PATH>          # Like `jekyll new` + `octopress init`
new post <TITLE>    # Add a new post to your site
new page <PATH>     # Add a new page to your site
new draft <TITLE>   # Add a new draft post to your site
publish <POST>      # Publish a draft from _drafts to _posts
unpublish <POST>    # Search for a post and convert it into a draft
isolate [POST]      # Stash all posts but the one you're working on for a faster build
integrate           # Restores all posts, doing the opposite of the isolate command
deploy              # deploy your site via S3, Cloudfront, Rsync, or to GitHub pages.
```

Run `octopress --help` to list sub commands and `octopress <subcommand> --help` to learn more about any subcommand and see its options.

### Init
Adds Octopress scaffolding to your site, create dir structure, config file, defaut theme.

### New
Add a new (post / page / draft) to your site.
```ruby
octopress new draft "Title of my draft"
```

### Publish a draft

Use the `publish` command to publish a draft to the _posts folder. This will also rename the file with the proper date format.

```sh
octopress publish _drafts/some-cool-post.md
octopress publish cool
```

| Option        | Description                                                                     |
| :--           | :--                                                                             |
| `--date DATE` | The date for the post. Should be parseable by [Time#parse](http://ruby-doc.org/stdlib-2.1.0/libdoc/time/rdoc/Time.html#method-i-parse) |
| `--slug SLUG` | Change the slug for the new post.                                               |
| `--dir DIR`   | Create post at _posts/DIR/.                                                     |
| `--force`     | Overwrite existing file.                                                        |

## Unpublish a post

Use the unpublish command to move a post to the _drafts directory, renaming the file according to the drafts convention.

```sh
octopress unpublish _posts/2015-01-10-some-post.md
octopress unpublish some post
```

### Isolate

The `isolate` command will allow you to stash posts in `_posts/_exile` where they will be ignored by Jekyll during the build process. Run `octopress integrate` to restore all exiled posts. This can be helpful if you have a very large site and you want to quickly preview a build for a single post or page.

```sh
octopress isolate                                # Move all posts
octopress isolate _posts/2014-10-11-kittens.md   # Move post at path
octopress isolate kittens                        # Move post matching search
```


### Deploy

You’ll have a repository named as yourusername.github.io, almost empty. You want to deploy your first article to that empty repository, but you before that you should know how github pages work.

Github pages expect to have two branches, the source branch and the master branch. The master branch is the one that github pages will show. The changes in the source branch won’t be published until you push your changes to master. A very simple workflow we use very often while coding, applied to publishing.

Before `octopress deploy` we need to generate the static site.

```ruby
jekyll generate
```

#### Master and Source branches

the *octopress deploy* command automatize a serie of task seeking for abreviation. It looks at `_deploy.yml` file where we set config the git repo, others default values should work. Among other task it make a **Git** `add`, `commit` and `push` command on each deploy. But what happend with our original files.

```
git checkout -b source
```

With this command we create a new git branch to keep versioning our source files. With that, you'll work in the source branch and deploy to the master branch.

```
git add --all
git commit -m "First commit for source files."
git push origin source
```

## Templates

Octopress post and page templates look like this.

```markdown
---
layout: {{ layout }}
title: {{ title }}
---
```

Dates get automatically added to a template for posts, and for pages if a --date option is set.

You can add to the YAML front matter, add content below and even even use liquid tags and filters from your site's plugins. There are a handful of local variables you can use when working with templates.

| Variable | Description                           |
| :--      | :--                                   |
| `date`   | The date (if set) or Time.now.iso8601 |
| `title`  | The title of the page (if set)        |
| `slug`   | The title in slug form                |
| `ymd`    | The date string, YYYY-MM-DD format    |
| `year`   | The date's year                       |
| `month`  | The date's month, MM                  |
| `day`    | The date's day, DD                    |

By default Octopress has templates for pages, posts and drafts. You can change them or create new ones for different types of content. To create linkposts template, add a file at _templates/linkpost, such as:

```
---
title: {{ title }}
external-url: {{ url }}
---
```

Then you can use it with a new post like this:
```sh
octopress new post "Some title" --template linkpost
octopress new post "Some title" -tm _templates/linkpost
```

In the second example, I'm passing the full template file path. This way I can use my shell's tab to auto-complete feature.

When creating templates, file name extensions are unnecessary since the files are just plain text anyway.


In the third example, if multiple posts match the search a prompt will ask you to select a post from a menu.

## Configuration

Octopress reads its configurations from `_config.yml`. Here's what the configuration looks like by default.

```ruby
# Default extension for new posts and pages
post_ext: markdown
page_ext: html

# Default templates for posts and pages
# Found in _templates/
post_layout: post
page_layout: page

# Format titles with titlecase?
titlecase: true

# Change default template file (in _templates/)
post_template: post
page_template: page
draft_template: draft
```

## Deployment

The Octopress gem comes with [octopress-deploy](https://github.com/octopress/deploy) which allows you to easily deploy your site with *Rsync*, on *S3* or *Cloudfront*, to GitHub pages, or other Git based deployment hosting platforms.

Basic usage
```sh
octopress deploy init METHOD [options]  # git, s3, or rsync - Create a _deploy.yml configuration
octopress deploy                        # Deploys _site based on deployment configuration
octopress pull DIR                      # Download your deployed site to the specified DIR
```

Examples for *github*, *s3* and *rsync*.
```sh
octopress deploy init git git@github.com:user/project
octopress deploy init s3
octopress deploy init rsync
```

You can find more detailed documentation by running:

```
octopress deploy --help
```

```sh
octopress build
```
This will generate your site in `_site` directory inside your `tech-blog` folder.

```sh
octopress deploy
```
This command should copy your `_site` folder content to a hidden folder `.deploy` which contains your git repository, and will add the changes, commit it and push it online.

Now you can see your blog live on `<username>.github.io`, if you ever want to use your custom domain name follow this extra step.

## Use your custom domain name (optional)

lets say you have a domain name _awesome-developer.com_, you will need to point your domain name to github ip addresses by adding 2 A records pointing to `192.30.252.153` and also to `192.30.252.154`, then point your domain name with a cname record to `<username>.github.io` also add a `CNAME` file -in capital letters- to your blog root directory the file should contain your domain name _awesome-developer.com_, then build and deploy your octopress blog.

## Embed GitHub Gists in your Jekyll or Octopress blog.

```ruby
group :jekyll_plugins do
    gem 'octopress', '~> 3.0'
    gem 'octopress-gist'
end
```

Install
```sh
bundle install
```

To embed Gist code use this syntax
```
{% gist [gist_id] [file_name] [options] %}
```

## Write beautiful fenced code snippets within any template.

```
gem 'octopress-codefence'
```

Add to Jekyll `_config.yml`

```
gems:
  - octopress-codefence
```

Syntax
```
lang [options]
```

| Options | Example | Description |
| :-- | :-- | :-- |
| `lang` | `ruby` | Used by the syntax highlighter. Passing 'plain' disables highlighting. |
| `title` | `title:"Figure 1.A"` | Add a figcaption title to your code block. |
| `link_text` | `link_text:"Download"` | Text for the link, default: `"link"`. |
| `linenos` | `lineos:false` | Disable line numbering. |
| `start` | `start:5` | Start the line numbering at the given value. |
| `mark` | `mark:1-4,8` | Highlight lines of code. This example marks lines 1,2,3,4 and 8 |
| `class` | `class:"css example"` | Add CSS class names to the code `<figure>` element |

### Include Code Snippets
Import files on your filesystem into any blog post as embedded code snippets with syntax highlighting and a download link. In the `_config.yml` you can set your `code_dir` but the default is `source/downloads/code`. Simply put a file anywhere under that directory and use the following tag to embed it in a post.

```
{% include_code [title] [lang:language] path/to/file [start:#] [end:#] [range:#-#] [mark:#,#-#] [linenos:false] %}
```

#### Basic options
* `[<title>]` - Add a custom figcaption to your code block (defaults to filename).
* `[lang:<language>]` - Force the syntax highlighter to use this language. By default the file extension is used for highlighing, but not all extensions are known by Pygments.

#### Additional options:

These options don't depend on any previous option and order does not matter.

* `[start:<#>]` - Render a snippet from the file beginning at the specified line
* `[end:<#>]` - Render a snippet from the file ending at the specified line
* `[range:<#-#>]` - Render a specified range of lines from a file (a shortcut for `start:#` `end:#`)
* `[mark:<#,#-#>]` - Mark one or more lines of code with the class name "marked". Accepts one number, *numbers separated by commas, and number ranges. Example `mark:1,5-8 ` will mark lines 1,5,6,7,8. *Note: If you've changed the beginning line number be sure these match rendered line numbers
* `[linenos:<false>]` - Do not add line numbers to highlighted code

## Build
```
bundle exec jekyll build
```

## Serve
```
bundle exec jekyll serve
```

## Code examples, statics and external services (jsbin, gist, codepen)

### [JSBin](https://github.com/jsbin/octopress-plugin)
Given a bin url, generates the embed code for jsbin with defined panels. Copy `jsbin.rb` into plugins dir of your Octopress project or clone it.

#### Installation
Move codepen.rb into the plugins folder at the root of your octopress repo.

#### Syntax
```
{% jsbin exedab %}
```

or specify which pannel will be loaded

```
{% jsbin exedab js,html %}
```

### [CodePen](https://github.com/riddla/octopress-codepen)

#### Installation
Move codepen.rb into the plugins folder at the root of your octopress repo.

#### Syntax
```
{% codepen href user [type] [height] %}
```


### Jekyll Sitemap Generator Plugin
Add `gem 'jekyll-sitemap'` to your site's *Gemfile* and run `bundle install`
Add the following to your site's `_config.yml`

```yml
gems:
  - jekyll-sitemap
```
#### Exclusions

If you would like to exclude specific pages/posts from the sitemap set the sitemap flag to false in the front matter for the page/post.

```yml
sitemap: false
```


## Aditional Info
[jekyll-example-with-heroku](https://github.com/markpundsack/jekyll-example-with-heroku-buildpack)
[Jekyll tutorial](https://www.andrewmunsell.com/tutorials/jekyll-by-example/tutorial)
More plugins and [3rd party code](https://github.com/imathis/octopress/wiki/3rd-party-plugins)
Deploy [Octopress deploy project](https://github.com/octopress/deploy) page.
[Full Documentation](https://github.com/octopress/octopress)
[Octopress-3](http://www.blazeboy.me/how-to-create-a-blog-like-this-for-free-octopress-3-scribble-theme-github.html)

