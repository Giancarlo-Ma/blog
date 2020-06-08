---
title: Hexo Introduction
date: 2018-10-03 10:16:53
categories:
- Hexo
---
This article focuses on how to use hexo to generate our static content management system, which is mostly from Youtube channel [Mike Dane](https://www.youtube.com/channel/UCvmINlrza7JHB1zkIOuXEbw).Thanks for him!
<!-- more -->

## Start

- When we install hexo on our PC, we can run `hexo new [filename]` command in the directory of hexo to create a new post.The new generated content will be in the source/_post folder.
- If we want to create a draft, we can run `hexo new draft [filename]` command in the directory of hexo.The new generated content will be in the source/_draft folder.
- If we want to show draft on our local server, we can run `hexo server --draft` command to run a server while displaying the draft article, but if we run `hexo server` command then we can only see the post article.
- If we want to draft file to be in post directory, we can use `hexo publish [draftname]`.
- Hexo also supports the third option which is page. Suppose we want to write a 'About me' page, then we can use `hexo new page [filename]`.
- According to above description, we find when we want to create a new post, we can only run `hexo new [filename]`. This default behavior is controlled by **_config.yml** file, which consists of a series of key-value pairs. In the writing section, we can change the default_layout. This must be one of the three options, which are post, draft or page.

## Front-matter

Front-matter is a block of YAML or JSON at the beginning of the file that is used to configure settings for your writings. Front-matter is terminated by three dashes when written in YAML or three semicolons when written in JSON.

We can use front-matter to define **title**, **date** and **tag** in one article. Front-matter is only meta data, which is data about data.

## Scaffold

Scaffold folder saves the scaffold information about what we will create. Seeing that when create a new post, draft or page, we even do not set the front-matter, there are always some contents. This is what the scaffold do for us.

In the scaffold folder, we can create our own scaffold. Attention to the front curly braces, which is placeholder for the generated file. We can also add `{{ layout }}` to get current layout of the file.

## Tags & Categories

Tags and categories are two different ways to organize files in hexo. Categories apply to posts in order, resulting in a hierarchy of classifications and sub-classifications. Tags are all defined on the same hierarchical level so the order in which they appear is not important.

## Tag Plugins

Tag plugins are a quick way to add some specific contents into ouy posts. We can use **'codeblock'** to begin a section of code, and **'endcodeblock'** to end the section of code. We can also add youtube video in our website using tag plugins.

## Asset Folder

We can put our own static files(i.e. images or PDF) into asset folder to have other people download them. But first we need to open **_config.yml** file and look at writing section, where we need to set the value of **post_asset_folder** to be true, which default is false.

After setting that, we can use asset folder to save our images or other static files, and can use curly braces grammar to get those resources. If we want to display the image itself, we can simply use **asset_img**. If we want a link to that image, we can use **asset_link**. If we want to get the path of that image, we can use **asset_path**. And after those three words, we simply put the filename of the image.

## Theme

The theme in hexo is only html, css and javascript files. We can link to official theme store to select one theme that we like, then we need to look at the theme's github page and clone that repository to our local theme folder. We can run `git clone [http-address] [themes/theme-name]`, then we can simply open our **_config.yml** file, then change the value of theme to the new theme name.

# Create our theme

To create our own theme, we can simply new folder under the themes folder and name it our name of theme. Last we modify the _config.yml file to make that theme display.
