+++
type = "post"
date = "2017-04-06T11:31:32-07:00"
title = "First Blog with Hugo"
draft = false

+++
When I was asked to create a blog site, I started researching the popular blogging platforms. However, I was getting overwhelmed while trying to understand the pros/cons of paid vs free platforms and the strategies of hosted vs self-hosted and the issues with each of them. That is when I came across [Hugo](http://hugo.spf13.com/) in a comment section of one of the blogs that I was reading. And, according to the comment, the only thing that was needed to create a site using [Hugo](http://hugo.spf13.com/) is a [Github](https://github.com) account. 

Since I already have a [Github](https://github.com) account, the comment appealed to the developer in me and I decided to investigate [Hugo](http://hugo.spf13.com/). As part of my investigation, when I learnt that [Hugo](http://hugo.spf13.com/) is not only open source and completely free but the generated site is also fast(because of static assets) and served directly off of [Github](https://github.com) using https://&lt;username>.github.io URL, I was sold! But, when I learnt that [Hugo](http://hugo.spf13.com/) allows content to be created using [Markdown](http://github.github.com/github-flavored-markdown/), I was blown away!

Having decided to create a static HTML site running on [Github](https://github.com) using [Hugo](http://hugo.spf13.com/), I wasn't sure how successful my efforts would be as I didn't know [Golang](https://golang.org/) and the thought of handcrafting HTML pages and CSS style classes wasn't too appealing. Then, I stumbled upon [Nate Finch](https://github.com/natefinch)'s excellent posts at [nfp.io](https://nfp.io) to make me a true believer in [Hugo](http://hugo.spf13.com/). Using the layouts and the theme templates from Nate's Github [repo](https://github.com/natefinch/npf) as the starting point, I was able to personalize them to create the code for my static HTML site. Though I have a long ways to go to automate things using Wercker for CI and other hooks, I think I am in much better shape as I got the basic stuff working.

This blog includes steps that one can take to create a static HTML site running on [Github](https://github.com) using [Hugo](http://hugo.spf13.com/). To be able to create a static HTML site on [Github](https://github.com), you must sign-up/register with [Github](https://github.com) and get a username. In the remainder of this post, treat `<username>` as a placeholder for your [Github](https://github.com) username.

## Install Hugo

Based on your operating system, you can follow these [instructions](https://gohugo.io/overview/installing/) to install [Hugo](http://hugo.spf13.com/) on your machine.

## Create Github Repositories

Sign into [Github](https://github.com) and create the following two public repositories and select the checkbox to initialize the repository with a README:

  * &lt;username>-hugo: This repository will contain the source code for your site such as templates, etc.
  * &lt;username>.github.io: Name of this repository must follow the convention. This repo will contain the actual artifacts such as HTML, CSS, etc. that will be served up when it is accessed using https://&lt;username>.github.io URL.

## Register Site with Disgus

[Hugo](http://hugo.spf13.com/) has inbuilt support for [Disgus](https://disqus.com/) to manage comments on any of the content  that is served from your generated static site. While registering your site with [Disgus](https://disqus.com/), you can specify the site name as either `<username>.github.io` or your own vanity domain name to create a [Disgus](https://disqus.com/) shortname. Then, all you need to do is specify the shortname in [Hugo](http://hugo.spf13.com/)'s `config.toml` using `disgusShortname` parameter.

## Setup Working Environment

Once the repositories are created, here are the steps for creating a working environment that would allow you to create content:

### Create Hugo Site

```
$ cd ~; mkdir Workdir; cd Workdir
$ hugo new site <username>-hugo
```

### Sync with the Git repo

```
$ cd ~/Workdir/<username>-hugo
$ git init
$ git remote add origin https://github.com/<username>/<username>-hugo
$ git branch --set-upstream-to=origin/master master
$ git pull origin master
```

### Create symbolic link

To generate the static HTML content, you will have to execute `hugo` command from `<username>-hugo` folder. By default, `hugo` generates the static artifacts in a sub-folder named `public`. These artifacts will have to merged/pushed to the `https://github.com/<username>/<username>.github.io` repository. To make this a bit easier, we can create a symbolic link as shown below:

```
$ cd ~/Workdir
$ git clone https://github.com/<username>/<username>.github.io.git
$ cd <username>-hugo
$ ln -s ../<username>.github.io public
$ echo public > .gitignore
```

### Copy Nate's Templates

To get started, you can copy Nate's templates as shown below:

```
$ cd /tmp
$ git clone https://github.com/natefinch/npf
$ cp -r /tmp/nfp/archetypes ~/Workdir/<username>-hugo/
$ cp -r /tmp/nfp/layouts ~/Workdir/<username>-hugo/
$ cp -r /tmp/nfp/themes ~/Workdir/<username>-hugo/
$ cp -r /tmp/nfp/static ~/Workdir/<username>-hugo/
```

### Update config.toml

You can update the minimal `~/Workdir/<username>-hugo/config.toml` that Hugo generated. For inspiration, you can look at Nate's `/tmp/npf/config.toml`. You should certainly update the following site attributes and parameters in:

  * `baseURL` 
  * `title` 
  * `description`
  * `disgusShortname` 
  * `copyright`
  * URLs to social media sites using your own details

### Update Other Files

Here are some of the other files that you need to update:

  * `~/Workdir/<username>-hugo/README.md`: Update the links and the repository names.
  * `~/Workdir/<username>-hugo/static/README.md`: This README.md will get copied to the `public` folder when the site is generated and will end up in the content root of your generated site. Update the links using your site specific details.
  * `~/Workdir/<username>-hugo/static/CNAME`: If you have a vanity domain name registered with a DNS provider, update this file with your domain name. This file also gets copied to the `public` folder when the site is generated and will end up in the content root of your generated site. If you do not have a domain name registered and are planning on using `https://<username>.github.io` to server your content, then you should delete this file.
  * `~/Workdir/<username>-hugo/themes/hyde/layouts/chrome/sidebar.html`: Update the links for social networking sites with your information. Also, update the copyright and other boilerplate information.
  * `~/Workdir/<username>-hugo/themes/hyde/layouts/chrome/head.html`: Replace reference to the obsoleted API `RSSlink` with `RSSLink` to avoid seeing an error while building the static site.

### Merge Changes

Once the scaffolding is created, you can push it to `https://github.com/<username>/<username>-hugo` repository as shown below:

```
$ cd ~/Workdir/<username>-hugo
$ git add .
$ git commit -m "Initial commit"
$ git push -u origin master
```

At this point, your environment is ready and you can create content.

## Add Hello World Blog

Now that you environment is all set, you can add the first blog entry using the following command:

```
$ cd ~/Workdir/<username>-hugo
$ hugo new blog/hello-world.md
$ cd content/blog
```

Edit `hello-world.md` by changing `draft = true` to `draft = false`. Also, update the `title` by making it more user friendly and use markdown syntax to add a title and text as shown below:

```
+++
type = "post"
date = "2017-04-07T19:32:12-07:00"
title = "Hello World using Hugo"
draft = false
+++

Hello World using Hugo
======================

My first flog with Hugo!
```

## Preview Content

Using Hugo's inbuilt server, you can preview the content using the following command:

```
$ cd ~/Workdir/<username>-hugo
$ hugo server --buildDrafts
```

Then, you can point your browser to `http://localhost:1313` to preview the content. You can click on the `Posts` link in the side-nav to look at the list view of your posts.

## Generate Content

Once satisfied, the content can be generated using the following commands:

```
$ cd ~/Workdir/<username>-hugo
$ hugo
```

This will result in the static HTML artifacts to be generated in the `public` sub-folder that is a symbolic-link to `../<username>.github.io` folder.

## Merge Changes

Note that the `hello-world.md` will be merged/pushed to `https://github.com/<username>/<username>-hugo` repository and the generated content in the `public` sub-folder will be merged/pushed to `https://github.com/<username>/<username>.github.io` repository.

Here are the steps to merge `hello-world.md` to `https://github.com/<username>/<username>-hugo` repository:

```
$ cd ~/Workdir/<username>-hugo
$ git add .
$ git commit -m "Added hello-world.md"
$ git push -u origin master
```

Here are the steps to merge generated artifacts to `https://github.com/<username>/<username>.github.io` repository:

```
$ cd ~/Workdir/<username>-hugo/public
$ git add .
$ git commit -am "First checkin with Hello World blog"
$ git push -u origin master
```

Load `https://<username>.github.io` in your browser and checkout your shiny little site! If you want to use a vanity domain name instead of `https://<username>.github.io`, then you need to register with a DNS provider and follow these [instructions](https://gohugo.io/tutorials/github-pages-blog#using-a-custom-domain) to add your custom domain to your Github site.

From this point on, whenever you need to add a new post, it's just lather, rinse, and repeat the aforementioned steps outlined for adding the Hello World blog!



