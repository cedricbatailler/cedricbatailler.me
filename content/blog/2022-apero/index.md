---
date: 2022-10-26

title: Quarto, Hugo, Apero
subtitle: How to set up a minimal (but pretty) website in the modern era.
author: Cédric Batailler

show_post_date: true
show_author_byline: true

draft: false

summary: |
    Follow me on a journey to build this website. The idea is to have a 
    system that has the fewest steps as  possible to go from a blog post on 
    my computer to a website living online. Among other, I discuss about 
    Quarto, Hugo, Github, and Netlify.

format: hugo

freeze: auto
---

I recently noticed that I probably don't spend as much time writing as I
should. *This is the kind of thoughts you can get by doing a
PhD*. This post is me trying to address this problem. Let's write more, let's
blog.

As a first post, I decided to offer a small tour of this website. Or rather,
of how it works. Let's go on an adventure!

# A Fresh New Start

{{< figure src="img/jan-kahanek-g3O5ZtRk2E4-unsplash.jpg" caption="Photo by Jan Kahánek." >}}

So, to start blogging. The first thing one needs to do so is a website. I had
one, but I wasn't really enthusiastic about it anymore. So all I had to do was
to build a new system that would work for me. Ideally, this system should
be minimal--meaning that it should have as few steps as possible between the
writing of a blog post and its posting, and it should be super easy to show
code running (because that's what I'd like to put here).

In the end, I decided to use some pieces of software that I had used
before and adopt some other. I would use [Quarto](https://quarto.org/)
to write the blog post and the code it contains, [Hugo](https://gohugo.io/) to
generate a static website, and to ditch my old-fashioned Academic theme for
the refreshing [Apéro](https://github.com/hugo-apero/). Here how it works.

## Quarto to Write and Run the Code

You probably won't be surprised to learn that the first step of blogging is
writing. And **I write in what is called Quarto documents**.

[Quarto](https://quarto.org/) is a relatively new piece of tech developed by
the amazing software engineers at [Posit](https://www.rstudio.com/)
(ex-RStudio). Basically, it offers a way to **combine text and code into a
single document**. You can run R, Python, or Julia from one place, and then
decide to turn the annotated results as a PDF file, a markdown document or even a
PowerPoint presentation.

The main reasons I picked Quarto are twofold. It is **familiar** and
**powerful**. First, familiar. Quarto looks a lot like the old
[Rmarkdown](https://rmarkdown.rstudio.com/) format I was used to[^1]. This
reduces by a fair amount what I would have to learn to
build the website. Second, it supports a lot of languages, and even though R
is--and will always remain--my first love, I have to say that I am looking
forward to playing with Python (now that I have figured out how to configure my
environment). Quarto appears to be an amazing tool for people with this
polyglot mindset.

In terms of organization, each of my blog post has its own folder which
contains a Quarto document where I write the post (an `"index.qmd"`). As I said
earlier, this document mixes some writing and some code and is used to
produce an output. When I am done with a post, a `quarto render` command
in my terminal will **produce a document with the code evaluated within**. In
other words, a Quarto containing a code chunk with a `read_csv(...)` call in
it, it will render a document that will show the actual content of the csv I want
to open.

If you want to know more about Quarto, and are familiar with rmarkdown, this
[blog post](https://www.apreshill.com/blog/2022-04-we-dont-talk-about-quarto/)
by Alison Hill is a great introduction!

The output I'm asking Quarto to produce is
[markdown](https://en.wikipedia.org/wiki/Markdown), which consists of a text
file with minimal formatting (you can put things in *italics*, in **bold**),
which is perfect for me because the actual pimping of the website won't
happen here.

The next step is to **turn these markdown files into something pretty**, and
build a website out of this.

## Hugo to Write the Website

A website, most of the time, is nothing more than some files in [HTML
format](https://en.wikipedia.org/wiki/HTML) put together and served on the
internet. And, **to turn my markdown files into a bunch of files that can be
served**, I decided to use is a
[static website generator](https://en.wikipedia.org/wiki/Static_site_generator)
called [Hugo](https://gohugo.io/).

The job of a static website generator, as the name suggests, is to build a
website whose content won't change very often. This is not the case of your
regular social network that has a feed changing any time you go there. Static
websites are mostly used for product pages, documentation, or blogs. A feature
that is especially interesting with static websites is that they do not require
a [backend](https://en.wikipedia.org/wiki/Frontend_and_backend) to run.
**Once the website built, it can live its life by itself forever**. This
should hopefully reduce the need for me to learn new things to
put the blog online.

So, back to [Hugo](https://gohugo.io/). The basic idea here is that it will
**take my collection of markdown files and turns them into a website**. For
Hugo to do its job, the only thing I had to do is to organize my files onto
my computer in a way that is understable. Basically, my blog posts needed
to be on a `content/` folder, and I had to put a config file in the root
directory.

In a nutshell, all I have to do now is run `hugo server` in a terminal for
**Hugo to build my website** and serve it onto a local server. How convenient?

Now, of course, there is a bit of tuning to do so that your website
actually looks like something. The first thing is to **pick a theme**.

I had run a website before with Hugo, and decided to ditch my old theme[^2] for
a fresh new one. I decided to go with the
[Apéro](https://github.com/hugo-apero/hugo-apero) theme because it was pretty,
but also because it was used by a looooot of people that inspire me
([Julia Silge](https://juliasilge.com/) or
[Jesse Mostipiak](https://www.jessemaegan.com/) to name a few). To use the
theme, the only thing I had to do is to put its content in a
`theme/` folder where my website lives[^3]. Once done, Hugo requires you to
[set up the theme in the config file](https://gohugo.io/getting-started/configuration/#theme)
and that's it. **My website was living on my computer**.

But of course, if the only place where my website lived was my computer, I
probably would quickly lose my interest in blogging.

## Send Everything Online

{{< figure src="img/jeremy-bezanger-LUjQCeKE0K0-unsplash.png" caption="Photo by Jeremy Bezanger." >}}

So far, I have a system that worked perfectly ... on my computer. Because I
am not the most functional human being (but who is), it is very likely that
I will lose all my interest in writing if no one can peer pressure me
about it.

To **share my website with the rest of the world**, I am used two tools, and
these tools will be the last one I will talk about (for now):
[GitHub](https://github.com/) and [Netlify](https://www.netlify.com/).

GitHub is probably one of the websites I used the most on a daily basis.
It serves two purposes in my workflow: I use it to **version and host** any
piece of software I write. This is a place where my website (or, rather, its
source code) exists. Netlify, the other tool, is (self-) described
as **a platform that can use what I put on GitHub to serve my website**. In
short, it takes the website's source code, runs the `hugo` command, and
serves the results on the whole internet.

With this setup, any change I make on my computer is then **pushed to
GitHub** if I want to, and then **lives on the internet within a few minutes**
(if not seconds).

And that's it, anybody can read what I write!

## Up to Work!

This setup kind of setup is not uncommon, and has the advantage to be very well
documented online. The Hugo team
[wrote about it](https://gohugo.io/hosting-and-deployment/hosting-on-netlify/),
the Netlify
[wrote about it](https://docs.netlify.com/integrations/frameworks/hugo/), and
if you want to bypass the Hugo step and go straight from Quarto to Netlify,
well the Quarto team
[wrote about it](https://quarto.org/docs/publishing/netlify.html). Overall,
setting up the website was a bit easier than I expected it. Already
having a website running with a lot of the same tools sure helped, but I
did spend more time looking for a pretty theme than working on the putting
the website online.

Now **the only thing left to do is blogging**. Remember, this was my original
motivation? And, if everything goes smoothly, you can expect me to document
some of my project right here! Fingers crossed. 🤞

> **Note**
>
> This blog post was originally written for an audience interested in
> building their website, but who would not know where to start.
> Maybe it was your case, maybe it was not. But now that you've read me,
> maybe you want to know more. If that's the case, you should totally have a look
> at [the actual code that builds this website](https://github.com/cedricbatailler/cedricbatailler.me/).

[^1]: Actually, Quarto and Rmarkdown play well together, and it was a 5 minutes
    job to convert an old Rmarkdown blog post into a new Quarto one.

[^2]: I used to run a website with the Academic theme which was very
    appropriate given my background. Recent changes in the project, however, added
    a tight integration with a publishing plateform that I didn't plan on using.
    To be honnest, I would not recommand using this theme now. The integration
    made the theme almost unusable.

[^3]: Actually, it is a simplification. I
    [forked](https://github.com/cedricbatailler/hugo-apero/) the original theme
    in Github so that I could make some minor tweaks, and I used this new repo as a
    [git subtree](https://www.atlassian.com/git/tutorials/git-subtree) in my
    project.
