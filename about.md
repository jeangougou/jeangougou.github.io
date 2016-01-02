---
layout: page
title: About
permalink: /about/
---

{% assign random = site.time | date: "%s%N" | modulo: 5  %} //site.data.inspirational-quotes.size
<blockquote>&ldquo;{{ site.data.inspirational-quotes[random].quote }}&rdquo; 
	<cite>{{ site.data.inspirational-quotes[random].person }}</cite></blockquote>

<p>
Hello everyone, I'm Jean. Work in IT, passionate and curious about most things.
	Martial arts fans, in permanent Musha Shugyo;
</p>


This is the base Jekyll theme. You can find out more info about customizing your Jekyll theme, as well as basic Jekyll usage documentation at [jekyllrb.com](http://jekyllrb.com/)

You can find the source code for the Jekyll new theme at:
{% include icon-github.html username="jglovier" %} /
[jekyll-new](https://github.com/jglovier/jekyll-new)

You can find the source code for Jekyll at
{% include icon-github.html username="jekyll" %} /
[jekyll](https://github.com/jekyll/jekyll)
