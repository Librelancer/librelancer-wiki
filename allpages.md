---
is_wiki_page: false
---
{% assign items = site.html_pages | sort: 'title' %}
<ul>
{% for page in items %}
{% assign title = page.title | default: page.name %}
{% if page.is_wiki_page != false and page.sitemap != false %}
<li><a href="{{ page.url | relative_url }}">{{title | escape}}</a></li>
{% endif %}
{% endfor %}
</ul>
