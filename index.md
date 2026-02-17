---
layout: single
title: "Det Lille Køkken"
header:
  overlay_image: "https://images.unsplash.com/photo-1504674900247-0877df9cc836?q=80&w=2070"
  overlay_filter: 0.5
  overlay_excerpt: "- Open Source Madviden"
---

<style>
  /* Grid-system til opskrifter */
  .recipe-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
    gap: 20px;
    margin-top: 20px;
  }
  .recipe-teaser {
    border: 1px solid #eee;
    border-radius: 8px;
    overflow: hidden;
    text-decoration: none;
    color: inherit;
    transition: transform 0.2s;
  }
  .recipe-teaser:hover {
    transform: scale(1.02);
    box-shadow: 0 4px 10px rgba(0,0,0,0.1);
  }
  .recipe-teaser img { 
    width: 100%;
    height: 150px;
    object-fit: cover;
  }
  .recipe-teaser h2 {
    font-size: 1.1em;
    padding: 10px;
    margin: 0;
  }
</style>

## Seneste opskrifter

<div class="recipe-grid">
  {% for opskrift in site.opskrifter limit: 6 %}
    <a href="{{ opskrift.url | relative_url }}" class="recipe-teaser">
      {% if opskrift.recipe_image %}
        <img src="{{ opskrift.recipe_image | relative_url }}" alt="{{ opskrift.title }}">
      {% else %}
        <img src="/assets/images/standardmad.png" alt="Ingen billede">
      {% endif %}
      <h2>{{ opskrift.title }}</h2>
    </a>
  {% endfor %}
</div>
