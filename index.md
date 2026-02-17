---
layout: home
title: "Det Lille Køkken"header:
  overlay_image: "https://images.unsplash.com/photo-1504674900247-0877df9cc836?q=80&w=2070"
  overlay_filter: 0.5
  overlay_excerpt: "- Open Source Madviden"
  ---
<style>
  /* Skjul hele topbjælken og fjern dens fylde kun på forsiden */
  .masthead {
    display: none !important;
  }
  /* Sørg for at hovedindholdet rykker helt op */
  .initial-content {
    margin-top: 0 !important;
  }


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
