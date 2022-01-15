---
title: Building a website with Jekyll and GitHub Pages
date: 2022-01-06 18:00:00 +0800
categories: [Programming]
tags: [web-development, html]
image:
  src: /assets/imgs/build-a-website.jpg
  width: 800
  height: 500
---

> An overview of the Jekyll framework, instructions about hosting your website on GitHub Pages, and a comparison among different options for writing technical blogs.

## The Jekyll Framework

![](/../assets/imgs/jekyll.png){: width="800" height="500" style="max-width: 70%"}

[Jekyll](https://jekyllrb.com/) is a website generator. It is a software package written in Ruby. To use it on your own machine, first install Ruby, and then install the package via Ruby gems:

```shell
gem install jekyll bundler
```

More detailed installation instructions are available on the official website:

[https://jekyllrb.com/](https://jekyllrb.com/)

With the framework, you don't have to write *bare* HTML code. You write your blog posts in markdown files, and structure layouts and elements of your website according to the framework. Then you can call  

```shell
jekyll s
```

and Jekyll will generate static website files (HTML, CSS, JS) that are ready to be served. 

### Liquid 

Define layouts of different pages of your website using the [Liquid](https://shopify.github.io/liquid/) template language. Liquid lets you write HTML pages in a programmable way. An example below is clear:

{% raw %}
```liquid
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
```
{% endraw %}

You have all the basics of a programming language, e.g. arithmetic operations, logic operators, control flows like `if`, `else `, `for`, as well as built-in functions (called "filters" in Liquid) like `abs` and  `upcase`. 

Jekyll also makes available some [variables](https://jekyllrb.com/docs/variables/) to reference data on your website. 

- The `site` variable for site wise information. For example, `site.posts` returns a list of all posts. You can define custom variables in `_config.yml`.  For example, with `foo: bar` defined in `_config.yml`, `site.foo` will have value `bar`. 
- The `page` variable for page specific information. For example, `page.title` is the title of the page, `page.url` is the relative url of the page. You can define custom variables in [front matter](https://jekyllrb.com/docs/front-matter/).

For a complete list of available variables go to [official docs on variables](https://jekyllrb.com/docs/variables/). 

### Site Structure

The basic structure in your website folder is typically as follows.

```
.
|-- /_includes
|-- /_layouts
|-- /_posts
|-- /_sass
|-- /assets
|-- _config.yml
|-- index.html
```

- The `_config.yml` file is where you put the metadata about the website, like url, site title, author, social links, and all other configurations. You can reference values defined here using the `site` variable.

- The `_posts` folder is where you put all your markdown blog posts.

- You may have several pages on your website, like a home page, an about page, a tag page showing all posts with some tag, or some other pages you wish to add. Define each page layout in the  `_layouts` folder.

- Define page styles in `_sass` folder.

- You can put all your files like images and pdf documents in an `assets` folder.

- You may wish to add some elements and functionalities to your website, like a banner, footer, paginator, word counts, share buttons and so on. You define each of them in `_includes` folder. Then you can include them in your layout by putting 

  {% raw %}
  ```liquid
  {% include something.html %}
  ```
  {% endraw %}

  in your layout file.

### Themes

At this point, you might have realized that, you still need to write a good amount of code if you want to build a full-fledged website. Luckily, many people have shared their website templates as [themes](https://jekyllrb.com/docs/themes/). You can directly use a theme so that you don't have to start from scratch and worry every details about HTML, CSS and JavaScript, but instead just focus on your content creation. View Jekyll themes at the following websites:

- [jamstackthemes.dev](https://jamstackthemes.dev/ssg/jekyll/)
- [jekyllthemes.org](http://jekyllthemes.org/)
- [jekyllthemes.io](https://jekyllthemes.io/)
- [jekyll-themes.com](https://jekyll-themes.com/)

On the other hand, it may be awkward to have your website look exactly the same as someone else's. After downloaded a theme, you can start messing around and modify the code in a theme for customization. Don't forget to put all the dependencies of a theme into your `Gemfile` or `_config.yml`. See the official docs "[converting gem-based themes to regular themes](https://jekyllrb.com/docs/themes/#converting-gem-based-themes-to-regular-themes)" for more details.

Finally, I should mention that when you initialize your web project using

```shell
jekyll new my-site
```

the [minima](https://github.com/jekyll/minima) theme will be installed by default. It is a good starter and is already good enough if you just want to write blogs and do not intend to add much other pages. You can easily customize your website based on the minima theme.

When you are finished developing your website, the next step is to push your folder to GitHub.

## Publish your site on GitHub Pages

Hosting your website on GitHub Pages is really easy. 

Name your repository *username.github.io* where username is your GitHub username. Push this repository to GitHub and you are done. Now you can open your browser, go to *uername.github.io* and see your website! There is no need to worry about web server or database or anything.

With a default GitHub account, the repository for your website has to be a public repository. If you change it to a private one, your published website will no longer be available. If you want to host your website in a private repository while still making your website available, you need to upgrade to a GitHub pro account, which costs you <mark>$4</mark> per month as of 2022. This is still cheaper than many website builder like wix and Squarespace, which would cost you around <mark>$12</mark> per month.

### Using a custom domain name
You can use your own domain name by putting the domain name in a file named `CNAME` in your repository. 

In addition, you need to tell your DNS provider to redirect your domain to GitHub servers. You DNS provider is usually the same as your domain provider, e.g. NameCheap, GoDaddy or Google Domains. After all, they don't know where you intent to host your website, being it AWS, Google Cloud, your own machine or GitHub. You have to tell them this information. To do so, login on your domain provider's website and create a `A` resource record, adding IP addresses for GitHub Pages.
```
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```

More instructions are at GitHub Pages [documentation](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site).

## Should you Drag and Drop?

Should you build your blog website using drag-and-drop website builders like wix and Squarespace? My point of view is, if you know programming, then you should use one of the frameworks like Jekyll (others include Gatsby, Hexo, Hugo). After all, you are building a static website for blogs, not a millionaire web app. You are not dealing with the backends. If you are satisfied with using a theme, just go ahead and you have almost no coding. If you want customization, it's really not that difficult to tweak a little bit HTML and CSS so you control exactly how your website looks. You can decide on your own where to host your website. You can host it via GitHub Pages, or cloud services like AWS and Google Cloud if you prefer. This flexibility can be very helpful if you decide to change your minds later.

If you simply want to write tech blogs, their price is probably a bit too expensive. It can be even more time-consuming to drag around and adjust elements until you are satisfied with the design. It is also difficult to save your work. What if you want to switch to another platform, like from wix to Squarespace, or from wix  to WordPress? Basically you have to start over again. But you never worry about losing your work if you have all your source code at your disposal.

## Should you write your blogs on Medium?

Medium is probably the most popular online publishing platform. With publications like [towards data science](https://towardsdatascience.com), it also has a large community of ML/AI lovers. There could be many potential benefits for writing blogs on medium. First, you can actually make money on medium, through the [Medium Partnership Program](https://medium.com/earn). For many people it's even their full-time job.  Second, you benefit from a large pool of audience. It's easier for people to come across your articles.  And third, you are free from all the hassles of writing code in order to build your website. You can just focus on your writings.

However, the biggest problem with medium for tech writers is that, <mark>they have zero support for LaTeX, as well as code highlighting.</mark> The reason is simple. The majority of users write or read about cars, food, emotions, money and more, but never differential equations or Python programming. People who need to write math equations occupy only a small portion.  Adding those supports means loading additional JS libraries, which can slow down the website and cause troubles for all users. They decide that it's not worthy to do that. The result in the end is that, your article could look *terrible*, especially if you have a lot of inline math and code snippets. Even though you can embed GitHub Gist for some code highlighting, the overall format quality for a math-intensive article is just too bad such that I wouldn't bother.

## Summary

In this post I introduced how to use Jekyll and GitHub Pages to build and host a static website. In Jekyll you can use the Liquid template language to write your HTML page layouts. Many themes are also available for free. Push your repo named _username.github.io_ to GitHub to serve your website to the public. I also compared developing your own website versus the option of using a third-party website builder or writing on medium. My suggestion is to develop your own site, since this gives you the ultimate flexibility and control.
