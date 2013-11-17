---
layout: page
title: Matthieu Rondeau Blog!
tagline: 
---
{% include JB/setup %}

<div class="row-fluid">
<div class="span6">
<div class="posts">
  <h2>Posts</h2>
  {% for post in site.posts %}
    <div>
    <h5>{{ post.date | date_to_string }}</h5>
    <h4><a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></h4>
    <p>{{ post.description }}</p>
    </div>
    <hr/>
  {% endfor %}
</div>
</div>
<div class="span6">
<h2>Welcome on my Blog</h2>

<p>My name is Matthieu Rondeau, I am a Web Mobile Engineer from France</p>

<p>I started this blog for several reasons :
<ul>
<li> I wanted to share some of my ideas of course</li>
<li> I wanted to test the Jekyll stuf.</li>
</ul>
</p>

<h2>Contents</h2>
<p>This is still a <strong>work in progress</strong>. But you will find several
things about Mobile and Web development.</p>


</div>
</div>
