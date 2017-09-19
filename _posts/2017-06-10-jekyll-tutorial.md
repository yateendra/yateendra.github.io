---
layout: post
title:  "How to create a free blog with Jekyll and GitHub Pages in 10 minutes"
description: Jekyll is a simple tool that transforms pages written in HTML, Textile or Markdown into a structured blog without the need of a database or dynamic code in the backend.
categories: jekyll
---

To host this site I used GitHub Pages and to generate the structure in the form of a blog I used Jekyll , a static site generator with the same functionalities as a CMS.

![](https://pbs.twimg.com/media/C4qXkC2WEAMGPZJ?format=jpg)

#### What is Jekyll?

[Jekyll](https://jekyllrb.com "Jekyll") is a simple tool that transforms pages written in HTML, Textile or Markdown into a structured blog without the need of a database or dynamic code in the backend .

#### What is GitHub Pages?

GitHub Pages is a static website hosting provided free by GitHub.

#### What does it take?

- PC with Linux or MacOS
- Account in GitHub
- 10 minutes
- Text editor for HTML like  Brackets , Atom , Sublime.

#### Create a repository on GitHub
- Log in to your GitHub account
- Create a new repository exactly under the name **username.github.io** , where **username** is your username (does not work if you do not enter your username exactly).
- Open the terminal `ctrl + alt + t` and clone the repository

`	
$ git clone https://github.com/username/username.github.io`

#### Choose a theme for your website [optional]

- Enter the repository of Jekyll themes and choose your favorite.
- Clone the theme
- Put the theme in your repository (do not forget that username is your username in GitHub).

`$ git clone https://github.com/rosario/kasper `
`$ mv kasper/* username.github.io `
`$ cd username.github.io`

Upload the files to your repository in GitHub it will ask for your username and password

`$ git add --all`
`$ git commit -m "post: Hello World!"`
`$ git push origin master`

 Access your new website The URL is exactly the name of the repository you created. Then, go to: username.github.io . If it is not yet available, wait a few minutes.

And Jekyll? Now that you have a working website, you need to learn how to create new posts. That's where Jekyll comes in.

To create a new post you must create a file inside the folder _postsnamed obeying the following format: YYYY-MM-DD-titulo-do-post.html for example  2015-01-14-hello-world.html. If not obeyed, Jekyll will not know how to organize his posts.

## Get to work:

#### Install Jekylll
`$ gem install jekyll`

Create a file called 2015-01-14-hello-world.html
`$ touch \_posts\2015-01-14-hello-world.html`
Add the following lines in the created file:
```
---
layout: post
title:  Hello World!
date:   2015-01-14 15:00:00
---
<h2>Jekyll</h2>
<p>Y Love You World s3</p>
```
To see how your page will be online, start a Jekyll server
`$ jekyll serve`

visit `localhost: 4000`

 Conquer the world! Repeat step # 3 and see your new post online. If you want to learn more about Jekyll, check out its [official documentation](http://jekyllrb.com/docs/home/ "official documentation") . It is very complete and easy to understand. It is worth taking a look.

Discuss below your experience with Jekyll and the tutorial.
