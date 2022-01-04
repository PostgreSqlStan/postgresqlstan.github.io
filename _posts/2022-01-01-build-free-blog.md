---
date: 2022-01-02
last_modified_at: 2022-01-03
title: "Build a blog site with a web browser"
category: Blog
tags:
  - GitHub Pages
  - Jekyll
  - Minimal Mistakes
excerpt: "Step by step directions for building a blog website using a web browser and GitHub Pages"
toc: true
header:
  teaser: /assets/teasers/cat-laptop.jpg
---

Using only a web browser, with [GitHub Pages](https://pages.github.com) and the [Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/) starter template, you can quickly and easily create a website with:

- sample posts, top navigation, author sidebar with social links, footer links, about page, and 404 page
- paginated home page blog
- archive pages for posts grouped by year, category, and tag
- site wide search

{% capture notice-1 %} 

**Requirements:** 
All you need is a web browser and GitHub account. Sign in or [create an account](https://github.com/signup) at GitHub if you don't have one already.
{% endcapture %}<div class="notice">{{ notice-1 | markdownify }}</div>

No technical knowledge is required. Continue reading to learn how.

## Clone the Starter Repo

Using the form below, simply choose a name and click the **Create** button to copy the starter template to your account. 

You can change the name later. I named mine "mm" for these examples.

* [Clone the Minimal Mistakes Starter Repo](https://github.com/mmistakes/mm-github-pages-starter/generate)

{% capture notice-2 %} 

To make the website available at https://*username*.github.io (where *username* is your username on GitHub), name your repo *username*.github.io.
{% endcapture %}<div class="notice--info">{{ notice-2 | markdownify }}</div>



## Enable GitHub Pages

Go to the **Settings** page for your new repo.

![Screenshot showing "Settings" button](/assets/ss/free-blog/0-repo-settings.jpg){:width="300"}

Scroll down a little to click on the **Pages** section.

![Screenshot showing "Settings" button](/assets/ss/free-blog/1-select-pages.jpg){:width="300"}

From the **Pages** section, select the Master branch as the source and click **Save**. (Don't select a theme.)

![Screenshot showing "Settings" button](/assets/ss/free-blog/2-github-pages-select-master.jpg){:width="600"}

That will enable your site, but nothing will be published until you commit changes to your repo.


## Personalize the site

{% capture notice-3%}

Navigating and editing files on GitHub is generally fairly intuitive. If you need help, see [Working with files](https://docs.github.com/en/repositories/working-with-files).
{% endcapture %}<div class="notice">{{ notice-3| markdownify }}</div>

You just need to edit a couple files and (optionally) upload a photo to personalize the site. 



### Edit \_config.yml

Edit `_config.yml` at the root of the repo, replacing the values for the following keys with your own information, starting with the site title and description:

```
title: Example Site
description: Example site description.
```

Scroll down a little to edit the author information. Leave the avatar unchanged for now. You can add a key/value for location if you wanted to display that as well.

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

Save and commit the changes, which will activate github-pages and render your website at the URL shown when you enabled GitHub Pages. It usually takes a minute or two to finish.

### Edit \_pages/about.md

Next, edit the About page, which is stored at `_pages/about.md`.

The page is in [Markdown](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax) format, a simple markup language used to add formatting elements to plaintext text documents. 

### Upload a bio photo

You can either change the name of the avatar image (by editing the value for `avatar` in `_config.yml`) or name your image "bio-photo.jpg". If you prefer not to have a photo, remove the entire line containing `avatar : "/assets/images/bio-photo.jpg"`.

To add your photo to the site, navigate to the `assets/images` folder and click on **Add file** to uplodad an image. A size of 200 x 200 pixels works best.

## Start blogging

The sample blog posts in the `_posts` folder demonstrate the date-based naming convention and the YAML front matter at the top of each post. Examine the examples and try creating your own. 

{% capture notice-5 %}

See [Jekyll - Posts](https://jekyllrb.com/docs/posts/) for more information.
{% endcapture %}<div class="notice">{{ notice-5 | markdownify }}</div>
