---
layout: page
---
{% include JB/setup %}

<title>bdep</title>
    
<ul class="posts">
	<table>
  	{% for post in site.posts %}
	<tr>
    	<td><li></li></td>
		<td><span>{{ post.date | date_to_string }}</span></td>
		<td>&raquo;</td>
		<td><a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></td>
	<tr>
  	{% endfor %}
  	</table>
</ul>
