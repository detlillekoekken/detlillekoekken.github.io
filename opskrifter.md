---
layout: single
title: "Find en opskrift"
permalink: /opskrifter/
---

<button class="filter-toggle-btn" onclick="toggleFilter()">
  <i class="fas fa-sliders-h"></i> Filtrér udvalg
</button>

<div id="filter-container" class="filter-box">
  <div class="filter-group-wrapper">
    
    {% comment %} Vi genererer listerne dynamisk fra site.opskrifter {% endcomment %}
    {% assign typer = "kategori,metode,indhold,svaerhedsgrad" | split: "," %}
    
    {% for type in typer %}
      <div class="filter-group">
        <h4>{% if type == "svaerhedsgrad" %}Sværhedsgrad{% else %}{{ type | capitalize }}{% endif %}</h4>
        <div class="filter-options">
          {% assign all_values = "" | split: "" %}
          
          {% for opskrift in site.opskrifter %}
            {% assign val = opskrift[type] %}
            {% if val %}
              {% if val.first %} {% comment %} Tjekker om det er en liste {% endcomment %}
                {% for item in val %}{% assign all_values = all_values | push: item %}{% endfor %}
              {% else %}
                {% assign all_values = all_values | push: val %}
              {% endif %}
            {% endif %}
          {% endfor %}
          
          {% assign unique_values = all_values | uniq | sort %}
          
          {% for value in unique_values %}
            <span class="filter-pill" data-type="{{ type }}" onclick="togglePill(this)">{{ value }}</span>
          {% endfor %}
        </div>
      </div>
    {% endfor %}

  </div>
  <button class="reset-link" onclick="resetFilters()">Nulstil alle filtre</button>
</div>

<hr>

{% assign sorterede_opskrifter = site.opskrifter | sort: "title" %}

<div class="recipe-grid" id="recipe-list">
  {% for opskrift in sorterede_opskrifter %}
    {% capture kat_list %}{{ opskrift.kategori | join: ',' | downcase }}{% endcapture %}
    {% capture met_list %}{{ opskrift.metode | join: ',' | downcase }}{% endcapture %}
    {% capture ind_list %}{{ opskrift.indhold | join: ',' | downcase }}{% endcapture %}
    {% assign s_grad = opskrift.svaerhedsgrad | default: opskrift.sværhedsgrad | downcase %}
    
    <div class="recipe-item" 
         data-kategori="{{ kat_list }}" 
         data-metode="{{ met_list }}" 
         data-indhold="{{ ind_list }}"
         data-svaerhedsgrad="{{ s_grad }}">
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
          <p class="recipe-tags">{{ opskrift.kategori | join: ' • ' }}</p>
        </div>
      </a>
    </div>
  {% endfor %}
</div>

<p id="no-results" style="display:none; text-align: center; margin-top: 50px;">Ingen opskrifter matcher de valgte filtre.</p>

<script>
function toggleFilter() {
  const container = document.getElementById('filter-container');
  container.classList.toggle('active');
}

function togglePill(element) {
  element.classList.toggle('active');
  filterRecipes();
}

function filterRecipes() {
  const activePills = document.querySelectorAll('.filter-pill.active');
  const filters = { kategori: [], metode: [], indhold: [], svaerhedsgrad: [] };

  activePills.forEach(pill => {
    filters[pill.dataset.type].push(pill.textContent.trim().toLowerCase());
  });

  const recipes = document.querySelectorAll('.recipe-item');
  let visibleCount = 0;

  recipes.forEach(recipe => {
    const rKategori = (recipe.getAttribute('data-kategori') || "").split(',');
    const rMetode = (recipe.getAttribute('data-metode') || "").split(',');
    const rIndhold = (recipe.getAttribute('data-indhold') || "").split(',');
    const rSvaer = recipe.getAttribute('data-svaerhedsgrad') || "";

    const matchKategori = filters.kategori.length === 0 || filters.kategori.some(f => rKategori.includes(f));
    const matchMetode = filters.metode.length === 0 || filters.metode.some(f => rMetode.includes(f));
    const matchIndhold = filters.indhold.length === 0 || filters.indhold.some(f => rIndhold.includes(f));
    const matchSvaer = filters.svaerhedsgrad.length === 0 || filters.svaerhedsgrad.includes(rSvaer);

    if (matchKategori && matchMetode && matchIndhold && matchSvaer) {
      recipe.style.display = 'block';
      visibleCount++;
    } else {
      recipe.style.display = 'none';
    }
  });

  document.getElementById('no-results').style.display = visibleCount === 0 ? 'block' : 'none';
}

function resetFilters() {
  document.querySelectorAll('.filter-pill').forEach(pill => pill.classList.remove('active'));
  filterRecipes();
}
</script>

<style>
  .filter-toggle-btn { background: #333; color: white; padding: 8px 16px; border: none; border-radius: 20px; cursor: pointer; font-size: 0.9em; margin-bottom: 20px; }
  .filter-box { display: none; background: #fff; padding: 20px; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1); border: 1px solid #eee; margin-bottom: 30px; }
  .filter-box.active { display: block; }
  .filter-group-wrapper { display: grid; grid-template-columns: repeat(auto-fill, minmax(160px, 1fr)); gap: 20px; }
  .filter-group h4 { margin: 0 0 10px 0; font-size: 0.75em; text-transform: uppercase; color: #aaa; letter-spacing: 0.5px; }
  .filter-options { display: flex; flex-wrap: wrap; gap: 6px; }

  .filter-pill { 
    background: #f5f5f5; border: 1px solid #eee; padding: 4px 10px; border-radius: 15px; 
    font-size: 0.8em; cursor: pointer; transition: background 0.2s; color: #555;
  }
  .filter-pill.active { background: #222; color: #fff; border-color: #222; }
  .reset-link { background: none; border: none; color: #d9534f; text-decoration: underline; cursor: pointer; margin-top: 20px; display: block; font-size: 0.85em; }

  .recipe-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(240px, 1fr)); gap: 20px; }
  .recipe-teaser { display: flex; flex-direction: column; height: 100%; border-radius: 10px; overflow: hidden; text-decoration: none; color: inherit; border: 1px solid #eee; transition: box-shadow 0.3s; }
  .recipe-teaser:hover { box-shadow: 0 5px 15px rgba(0,0,0,0.1); }
  .recipe-img-container { width: 100%; height: 160px; overflow: hidden; }
  .recipe-img-container img { width: 100%; height: 100%; object-fit: cover; }
  .recipe-info { padding: 15px; background: white; }
  .recipe-info h3 { margin: 0; font-size: 1.1em; }
  .recipe-tags { font-size: 0.8em; color: #999; margin-top: 5px; }
</style>
