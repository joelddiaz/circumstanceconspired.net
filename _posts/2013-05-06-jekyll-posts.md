---
layout: post_entry
image: /img/bikes.jpg
title: Displaying your posts with jekyll
---

You've got jekyll generating your static site. Now you need to programatically handle your posts so that jekyll automates the creation of the posts themselves and the pages listing the posts.
<!--more-->

In your directory holding your site, you should have a few files and directories with '_'s (ie. '_layout' and '_site'). Jekyll can make use of a directory for housing each post for the site as long as its in the '_posts' directory. Go ahead and make the directory and put a file named 2013-05-01-first-post.md with the content below:

    ---
    layout: post_entry
    title: First Jekyll Post
    ---

    Hello world! This is my first post.

With this post we are going to use the (presently non-existent) post_entry layout and generate a page with the content below.  As an aside, the '.md' extension is for 'Markdown' a way to write text for use on a web page without having to worry about HTML.

Now go ahead and create the 'post_entry' layout referenced in '_layouts/post_entry.html'.

    ---
    layout: default
    ---

    <h2> {{"{{ page.title "}}}} </h2>
    <br>
    <h3> {{"{{ page.date | date: "%Y-%d-%m" "}}}} </h3>

    {{"{{ content "}}}}

The 'post_entry' layout will use the previously created 'default' as its layout, but in the \{\{ content \}\} area of the 'default' layout 'post_entry' will add your post's title and date before the actual post content. This way, each post will inherit the general layout of the website (assuming all pages share the 'default' layout that is). Now run `jekyll --server` and open up <http://localhost:4000/2013/05/01/first-post.html>, and you should see something that looks like this:

![Your 1st post](/img/2013-05-06-first-post.png)

While <http://localhost:4000/2013/05/01/first-post.html> is a perfectly reasonable looking link, you want someone visiting your home page to be able to find your posts. For this to happen, you'll want to modify your main 'index.html' file to look like:

    ---
    layout: default
    custom_year: 2000
    ---
    <section class="content">
     Content of main page.

    <ul class="post_entries">
     {{"{% for post in site.posts limit:10 "}}%}
      <li>
       <h2> {{"{{ post.date | date: "%Y-%m-%d" "}}}}
       <a href="{{"{{ post.url "}}}}"> {{"{{ post.title "}}}} </a>
      </li>
     {{"{% endfor "}}%}
    </ul>

    </section>

What's been added is an unordered list where each list item contains a post. It will only show the first ten posts from your entire list of posts (sorted with newest first). Each list item will contain the date of the post, and the title of the post which will be clickable to take you to the post itself.

The mysterious "%Y-%m-%d" is a way to format the date into a human readable format (YYYY-MM-DD in this instance). You can change it to print out the month names with a formatting style of "%B %d, %Y". More info on the date formatting options can be found at <http://www.ruby-doc.org/stdlib-2.0/libdoc/date/rdoc/Date.html#method-i-strftime>.

Rerun `jekyll --server` and view the output of your main page <http://localhost:4000>. You should see your one and only post listed.

![index with post](/img/2013-05-06-index-with-post.png)

Last thing you'll want is a page showing more than just the most recent ten posts. Create an 'archive.html' file that will look very similar to your current 'index.html':

    ---
    layout: default
    title: Archive of posts
    ---
    <section class="content">
     Archive of posts.
    <ul class="post_entries">
     {{"{% for post in site.posts "}}%}
      <li>
       {{"{{ post.date | date: "%Y-%m-%d" "}}}}
	<a href="{{"{{ post.url "}}}}"> {{"{{ post.title "}}}} </a>
      </li>
     {{"{% endfor "}}%}
    </ul>

    </section>

The main difference here is that we aren't setting 'limit: 10' in the 'for' loop that goes through each of your posts like we did for the main 'index.html' page. Regenerate your site and visit <http://localhost:4000/archive.html> to see your handiwork.

![archive page](/img/2013-05-06-archive-page.png)
