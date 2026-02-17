---
layout: single
title: "Det Lille Køkken"
header:
  overlay_image: "https://images.unsplash.com/photo-1504674900247-0877df9cc836?q=80&w=2070"
  overlay_filter: 0.5
  overlay_excerpt: "- Open Source Madviden"
---

## Seneste opskrifter

<div class="recipe-grid">
  {% assign seneste = site.opskrifter | sort: "date" | reverse %}
  {% for opskrift in seneste limit: 3 %}
    <div class="recipe-item">
      <a href="{{ opskrift.url | relative_url }}" class="recipe-teaser">
        <div class="recipe-img-container">
          {% if opskrift.recipe_image %}
            <img src="{{ opskrift.recipe_image | relative_url }}" alt="{{ opskrift.title }}">
          {% else %}
            <img src="{{ '/assets/images/standardmad.png' | relative_url }}" alt="Billede mangler">
          {% endif %}
        </div>
        <div class="recipe-info">
          <h3>{{ opskrift.title }}</h3>
        </div>
      </a>
    </div>
  {% endfor %}
</div>

<div class="see-more-container">
  <a href="{{ '/opskrifter/' | relative_url }}" class="see-more-link">
    Se flere opskrifter <i class="fas fa-arrow-right"></i>
  </a>
</div>

<style>
  /* Grid Layout - Præcis som på opskriftssiden */
  .recipe-grid { 
    display: grid; 
    grid-template-columns: repeat(2, 1fr); 
    gap: 15px;
    justify-content: center;
    margin-top: 20px;
  }

  @media (max-width: 767px) {
    .recipe-grid { grid-template-columns: repeat(2, minmax(0, 180px)); }
  }

  @media (min-width: 768px) {
    .recipe-grid { 
      grid-template-columns: repeat(auto-fill, 180px); 
      gap: 20px;
    }
  }

  /* Kort Styling */
  .recipe-teaser { 
    display: flex; 
    flex-direction: column; 
    height: 100%; 
    border-radius: 6px; 
    overflow: hidden; 
    text-decoration: none; 
    color: inherit; 
    border: 1px solid #eee; 
    background: #fff; 
    transition: transform 0.2s; 
  }
  
  .recipe-teaser:hover { transform: translateY(-3px); box-shadow: 0 4px 10px rgba(0,0,0,0.05); }

  /* 4:3 Ratio på billedbeholderen */
  .recipe-img-container { 
    width: 100%; 
    aspect-ratio: 4 / 3; 
    position: relative;
    overflow: hidden; 
    background: #f9f9f9; 
  }

  .recipe-img-container img { 
    position: absolute;
    top: 0;
    left: 0;
    width: 100%; 
    height: 100%; 
    object-fit: cover; 
    margin: 0 !important; /* Fjerner eventuel default margin fra temaet */
  }

  .recipe-info { 
    padding: 8px; 
    flex-grow: 1; 
    text-align: center; 
    display: flex; 
    align-items: center; 
    justify-content: center; 
  }

  .recipe-info h3 { 
    margin: 0; 
    font-size: 0.85em; 
    line-height: 1.2; 
    color: #333; 
    font-weight: 600; 
  }

  /* Se flere link styling */
  .see-more-container { 
    text-align: right; 
    margin-top: 25px; 
  }

  .see-more-link { 
    color: #000; 
    text-decoration: none; 
    font-size: 0.9em; 
    font-weight: 600;
    border-bottom: 1px solid transparent;
    transition: border 0.2s;
  }

  .see-more-link:hover { 
    border-bottom: 1px solid #000;
  }
</style>
