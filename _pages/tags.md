---
title: "Tags"
permalink: /tags/
layout: single
---

{% comment %}
<!-- Collect all unique tags from all posts -->
{% endcomment %}
{% assign all_tags = "" | split: "," %}
{% for post in site.posts %}
  {% for tag in post.tags %}
    {% assign tag_exists = false %}
    {% for existing_tag in all_tags %}
      {% assign existing_lower = existing_tag | downcase %}
      {% assign current_lower = tag | downcase %}
      {% if existing_lower == current_lower %}
        {% assign tag_exists = true %}
        {% break %}
      {% endif %}
    {% endfor %}
    {% unless tag_exists %}
      {% assign all_tags = all_tags | push: tag %}
    {% endunless %}
  {% endfor %}
{% endfor %}

{% comment %}
<!-- Sort tags alphabetically (case-insensitive) -->
{% endcomment %}
{% capture tag_sort_string %}
  {% for tag in all_tags %}{{ tag | downcase }}#{{ tag }}{% unless forloop.last %},{% endunless %}{% endfor %}
{% endcapture %}
{% assign tag_hashes = tag_sort_string | split: ',' | sort %}
{% assign sorted_tags = "" | split: "," %}
{% for hash in tag_hashes %}
  {% assign keyValue = hash | split: '#' %}
  {% assign sorted_tags = sorted_tags | push: keyValue[1] %}
{% endfor %}

<div class="tags-list">
  {% for tag in sorted_tags %}
    <div class="tag-section" id="{{ tag | slugify }}">
      <h2 class="tag-title">{{ tag }}</h2>
      <ul class="posts-list">
        {% for post in site.posts %}
          {% if post.tags contains tag %}
            <li class="post-item">
              <a href="{{ post.url }}" class="post-link">
                <span class="post-title">{{ post.title }}</span>
                <span class="post-date">{{ post.date | date: "%B %d, %Y" }}</span>
              </a>
            </li>
          {% endif %}
        {% endfor %}
      </ul>
    </div>
  {% endfor %}
</div>

<style>
.tags-list {
  margin-top: 2rem;
}

.tag-section {
  margin-bottom: 3rem;
}

.tag-title {
  font-size: 1.5rem;
  margin-bottom: 1rem;
  color: #333;
  border-bottom: 2px solid #e8e8e8;
  padding-bottom: 0.5rem;
}

.posts-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.post-item {
  margin-bottom: 0.5rem;
}

.post-link {
  display: flex;
  justify-content: space-between;
  align-items: center;
  text-decoration: none;
  color: #333;
  padding: 0.5rem;
  border-radius: 4px;
  transition: background-color 0.2s ease;
}

.post-link:hover {
  background-color: #f5f5f5;
  text-decoration: none;
}

.post-title {
  font-weight: 500;
}

.post-date {
  font-size: 0.9rem;
  color: #666;
}
</style>
