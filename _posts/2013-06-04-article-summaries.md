---
layout: post_entry
image: /img/bikes.jpg
title: Article summaries/excerpts
---

Your front page now lists a certain number of your most recent posts with the rest available in the archive.  Let's figure out how to add article summaries (or excerpts).

<!--more-->

Jekyll doesn't have any native way of flagging the summary or first paragraph (or whatever) that I'm aware of.  So we're going to extend jekyll with some basic Ruby code to accomplish this.

Make a directory '_plugins' at the root of your website, and put a new file named 'more.rb' (thank you [stack overflow](http://stackoverflow.com/questions/10859175/how-to-show-a-preview-of-a-post-using-jekyll-bootstrap-theme)) in there:

    module More
	    def more(input, type)
		    if input.include? "<!--more-->"
			    if type == "excerpt"
				    input.split("<!--more-->").first
			    elsif type == "remaining"
				    input.split("<!--more-->").last
			    else
				    input
			    end
		    else
			    input
		    end
	    end
    end

    Liquid::Template.register_filter(More)

This defines a new Liquid filter (called 'more') that can do two things.

First, it can take in the entire contents of a post and return the whole post back unchanged (the default behavior).  You don't need to do anything special beyond pass the 'more' module whatever text you want it to return back.  Not terribly interesting.

The other behavior is triggered when you send the the text "excerpt" in addition to the entire post contents (or any content really).  It will go through the text and split it into two halves based on the existence of the the text '&lt;!&ndash;&ndash;more&ndash;&ndash;&gt;'.  If you send it

    First paragraph.

    Second paragraph.

    <!--more-->

    Third.

    Etc.

It will return all the text above '&lt;!&ndash;&ndash;more&ndash;&ndash;&gt;' when you ask for the "excerpt" version of the text.

With this new capability, you can now change your 'index.html' (and 'archive.html' too) to provide the article summary in addition to the date and title.  'index.html' should look something like:

    ---
    layout: default
    custom_year: 2000
    ---
    <section class="content">
     Content of main page.

    <ul class="post_entries">
     {{"{% for post in site.posts limit:10 "}}%}
      <li>
       {{"{{ post.date | date: "%Y-%m-%d" "}}}}
	<a href="{{"{{ post.url "}}}}"> {{"{{ post.title "}}}} </a>
	{{"{{ post.content | more: "excerpt" "}}}}
      </li>
     {{"{% endfor "}}%}
    </ul>

    </section>

Notice how each '&lt;li&gt;' item now has '\{\{ post.content | more: "excerpt" \}\}' to display the excerpt/summary with each post entry.

You'll need to modify your actual post(s) with '&lt;!&ndash;&ndash;more&ndash;&ndash;&gt;' to indicate where to have the 'more' module split the post.

    ---
    layout: post_entry
    title: First Jekyll Post
    ---

    Hello world! This is my first post.

    <!--more-->

    And this is the second paragraph in the first post.

Fire up `jekyll --server` and view the results <http://localhost:4000>:

![articles with excerpts](/img/2013-06-04-index-excerpt.png)

Web browsers will treat any text inside of opening tag '&lt;!&ndash;&ndash;' and closing tag '&ndash;&ndash;&gt;' as comments so the '&lt;!&ndash;&ndash;more&ndash;&ndash;&gt;' text is invisible in your actual post's content.

![actual article](/img/2013-06-04-article.png)
