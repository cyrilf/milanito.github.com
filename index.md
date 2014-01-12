---
layout: page
title: Welcome on my Blog
tagline: 
---
{% include JB/setup %}
<div id="slide1">
	<div class="row-fluid">
		<div class="container-narrow">
			<div class="row-fluid index-row">
				<div class="span12">
					<h1>{{ page.title }} {% if page.tagline %} <small>{{ page.tagline }}</small>{% endif %}</h1>
					<p>My name is Matthieu Rondeau, I am a Web Mobile Engineer from France. 
					This is still a <strong>work in progress</strong>. But you will find several
					things about Mobile and Web development.</p>
					<p>I started this blog for several reasons :
					<ul>
					<li> I wanted to share some of my ideas of course</li>
					<li> I wanted to test the Jekyll stuf.</li>
					</ul>
					</p>
				</div>
			</div>
		</div>
	</div>
</div>

<div id="slide2">
	<div class="row-fluid">
		<div class="span12">
			<div class="posts">
			 	<div class="row-fluid">
			 		<div class="span12">
			 			<div class="container-narrow">
			 				<div class="row-fuild">
			 					<div class="span12">
			 						<h2>Recent Posts</h2>
			 						<hr>
			 					</div>
			 				</div>
			 			</div>
			 		</div>
			 	</div>
				{% assign countLine = 1 %}
				<div class="row-fluid">
					{% for post in site.posts %}
					{% if forloop.index0 == 4 * countLine %}
					</div>
					<div class="row-fluid">
					{% assign countLine = countLine + 1 %}
					{% endif %}
					<div class="span3">
						<div class="row-fluid">
							<div class="span12">
								<h4><a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></h4>
							</div>
						</div>
						<div class="row-fluid">
							<div class="span12">
								<h6>{{ post.date | date_to_string }}</h6>
							</div>
						</div>

						<div class="row-fluid">
							<div class="span12">
								<p>
									<small>
										{{ post.description }}
									</small>
								</p>
							</div>
						</div>
						<div class="row-fluid">
							<div class="span12">
								<a href="{{ BASE_PATH }}{{ post.url }}" class="btn readmore">Read More ...</a>
							</div>
						</div>
					</div>
					{% assign count = count + 1 %}
					{% endfor %}
				</div>
			</div>
		</div>
	</div>
</div>


