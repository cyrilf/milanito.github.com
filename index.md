---
layout: page
title: Matthieu Rondeau Blog!
tagline: 
---
{% include JB/setup %}

## Posts

<div class="posts">
  {% for post in site.posts %}
    <div>
    <h5>{{ post.date | date_to_string }}</h5>
    <h4><a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></h4>
    <p>{{ post.description }}</p>
    </div>
    <hr/>
  {% endfor %}
</div>

## Welcome on my Blog

I started this blog for several reasons :
* I wanted to share some of my ideas of course
* I wanted to test the Jekyll stuf.
This is of course still in progress but I am working on it

## Credits
This site has been made using [Jekyll Bootstrap](http://jekyllbootstrap.com).




