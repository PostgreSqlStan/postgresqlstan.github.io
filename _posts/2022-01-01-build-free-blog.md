---
title: "Build a blog site with a web browser."
category: Blog
tags:
  - GitHub Pages
excerpt: "Step by step directions for building a blog website using a web browser and GitHub Pages"
toc: true
header:
  teaser: /assets/teasers/cat-laptop.jpg
---

Using only a web browser, with [GitHub Pages](https://pages.github.com) and the [Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/) starter template, you can quickly and easily create a website with:

- Sample posts, top navigation, author sidebar with social links, footer links, about page, and 404 page.
- Paginated home page blog.
- Archive pages for posts grouped by year, category, and tag.
- Site wide search.

{% capture notice-1 %} 

**Requirements:** 
All you need is a web browser and GitHub account. Sign in or [create an account](https://github.com/signup) at GitHub if you don't have one already.

No technical knowledge is required.

{% endcapture %}<div class="notice">{{ notice-1 | markdownify }}</div>

Continue reading to learn how.

## Clone the Starter Repo

Use this form below to copy the starter template to your account:

* [Clone the Minimal Mistakes Starter Repo](https://github.com/mmistakes/mm-github-pages-starter/generate)

{% capture notice-2 %} 

To make the website available at https://*username*.github.io (where *username* is your username on GitHub), name your repo *username*.github.io.
{% endcapture %}<div class="notice--info">{{ notice-2 | markdownify }}</div>

You can change the name of the cloned repo later. I named mine "mm" for these examples.

## Enable GitHub Pages

Go to the **Settings** page for your new repo.

![Screenshot showing "Settings" button](/assets/ss/free-blog/0-repo-settings.jpg){:width="300"}

Scroll down a little to click on the **Pages** section.

![Screenshot showing "Settings" button](/assets/ss/free-blog/1-select-pages.jpg){:width="300"}

From the **Pages** section, select the Master branch as the source and click **Save**. (Don't select a theme.)

![Screenshot showing "Settings" button](/assets/ss/free-blog/2-github-pages-select-master.jpg){:width="600"}

## Personalize the site

You just need to edit a couple files and (optionally) upload a photo to personalize the site. (See [Editing files](https://docs.github.com/en/repositories/working-with-files/managing-files/editing-files) for help editing files on GitHub.)

### Edit \_config.yml

Edit the `_config.yml` file at the root of the repo, replacing the values for the following keys with your own information, starting with the site title and description:

```
title: Example Site
description: Example site description.
```

Scroll down a little to the author information. Leave the avatar unchanged for now. If you want a location displayed, add a key/value for that:

```
author:
  name   : "Your Name"
  avatar : "/assets/images/bio-photo.jpg"
  bio    : "Example bio."
  location: "Earth"
```

{% capture notice-4 %}

**Warning**: Be careful about indentation, which must consist of two spaces, not tabs. See the [YAML Tutorial](https://www.cloudbees.com/blog/yaml-tutorial-everything-you-need-get-started) for more information about formatting rules. 
{% endcapture %}<div class="notice--warning">{{ notice-4 | markdownify }}</div>
  
In the links sub-section, either edit the url values with your own or delete the existing entries (the entire label, icon, and url lines):
  
```  
  links:
    - label: "Twitter"
      icon: "fab fa-fw fa-twitter-square"
      url: "https://twitter.com/postgresqlstan"
```

Do the same for the links in the footer section:

```
footer:
  links:
    - label: "Twitter"
      icon: "fab fa-fw fa-twitter-square"
      url: "https://twitter.com/postgresqlstan"
```

Save and commit the changes.

### Edit \_page/about.md

Personalize the About page by editing the [Markdown](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax) file at `_pages/about.md` in the repo.

### Upload a bio photo

You can change the name of the bio photo by editing `_config.yml` or match the existing file name (`bio-photo.jpg`). If you prefer not to have a photo, remove the entire line containing `  avatar : "/assets/images/bio-photo.jpg"`.

To add your photo to the site, navigate to the `assets/images` folder and click on **Add file : Upload Files**. A size of 200 x 200 pixels works best.

## Start blogging

You can figure out how to make a blog post by examining the Markdown files in the `_posts` folder of the repo. See [Jekyll - Posts](https://jekyllrb.com/docs/posts/) for more information about blog posts.

