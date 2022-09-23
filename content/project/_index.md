---
title: Learn about Hugo Apéro
description: |
  Learn how to use Hugo Apéro to build a personal website.
author: "Alisson Hill"
show_post_thumbnail: true
show_author_byline: true
show_post_date: true
# for listing page layout
layout: list-sidebar # list, list-sidebar, list-grid

# for list-sidebar layout
sidebar: 
  title: But what is he doing?
  description: |
    A collection of projects which made the final cut. Have a look.

# set up common front matter for all pages inside blog/
cascade:
  author: "Cédric Batailler"
  show_author_byline: true
  show_post_date: true
  show_comments: true # see site config to choose Disqus or Utterances
  layout: single-sidebar
  # for single-sidebar layout
  sidebar:
    text_link_label: View all projects
    text_link_url: /projects/
    show_sidebar_adunit: false # show ad container
---