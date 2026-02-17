---
layout: single
title: "Find en opskrift"
permalink: /opskrifter/
---

<div class="filter-header-actions">
  <button class="filter-toggle-btn" onclick="toggleFilter()">
    <i class="fas fa-sliders-h"></i> Filtrér udvalg
  </button>

  <div id="recipe-counter" class="counter-pill">
    <i class="fas fa-utensils"></i> <span id="counter-text">{{ site.opskrifter | size }} tilgængelige opskrifter</span>
  </div>
</div>

<div id="filter-container" class="filter-box">
  <div class="filter-group-wrapper">
    
    {% assign typer = "kategori,metode,indhold,svaerhedsgrad" | split: "," %}
    
    {% for type in typer %}
      <div class="filter-group">
        <h4>{% if type == "svaerhedsgrad" %}Sværhedsgrad{% else %}{{ type | capitalize }}{% endif %}</h4>
        <div class="filter-options">
          {% assign all_values = "" | split: "" %}
          {% assign has_empty = false %}
          
          {% for opskrift in site.opskrifter %}
            {% assign val = opskrift[type] %}
            {% if val == nil or val == "" or val == empty %}
              {% assign has_empty = true %}
            {% else %}
              {% if val.first %}
                {% for item in val %}
                  {% assign cleaned_item = item | strip | downcase %}
                  {% assign all_values = all_values | push: cleaned_item %}
                {% endfor %}
              {% else %}
                {% assign cleaned_item = val | strip | downcase %}
                {% assign all_values = all_values | push: cleaned_item %}
              {% endif %}
            {% endif %}
          {% endfor %}
          
          {% assign unique_values = all_values | uniq | sort %}
          
          {% for value in unique_values %}
            <span class="filter-pill" data-type="{{ type }}" onclick="togglePill(this)">{{ value | capitalize }}</span>
          {% endfor %}

          {% if has_empty %}
            <span class="filter-pill" data-type="{{ type }}" data-io="true" onclick="togglePill(this)">I/O</span>
          {% endif %}
        </div>
      </div>
    {% endfor %}

  </div>
  
  <div class="filter-footer">
    <button class="reset-link" onclick="resetFilters()">Nulstil alle filtre</button>
    <span class="footer-separator">|</span>
    <button class="reset-link" onclick="toggleFilter()">Luk dialogboks</button>
  </div>
</div>

<hr>

{% assign sorterede_opskrifter = site.opskrifter | sort: "title" %}

<div class="recipe-grid" id="recipe-list">
  {% for opskrift in sorterede_opskrifter %}
    {% capture kat_list %}{{ opskrift.kategori | join: ',' | downcase }}{% endcapture %}
    {% capture met_list %}{{ opskrift.metode | join: ',' | downcase }}{% endcapture %}
    {% capture ind_list %}{{ opskrift.indhold | join: ',' | downcase }}{% endcapture %}
    {% assign s_grad = opskrift.svaerhedsgrad | default: opskrift.sværhedsgrad | downcase | strip %}
    
    <div class="recipe-item" 
         data-kategori="{{ kat_list | default: 'i/o' }}" 
         data-metode="{{ met_list | default: 'i/o' }}" 
         data-indhold="{{ ind_list | default: 'i/o' }}"
         data-svaerhedsgrad="{{ s_grad | default: 'i/o' }}">
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

function updateCounter(count) {
  const counterText = document.getElementById('counter-text');
  if (count === 0) {
    counterText.textContent = "Ingen tilgængelige opskrifter";
  } else if (count === 1) {
    counterText.textContent = "1 tilgængelig opskrift";
  } else {
    counterText.textContent = count + " tilgængelige opskrifter";
  }
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

  updateCounter(visibleCount);
  document.getElementById('no-results').style.display = visibleCount === 0 ? 'block' : 'none';
}

function resetFilters() {
  document.querySelectorAll('.filter-pill').forEach(pill => pill.classList.remove('active'));
  filterRecipes();
}

updateCounter({{ site.opskrifter | size }});
</script>

<style>
  /* Header Actions Layout */
  .filter-header-actions { display: flex; align-items: center; gap: 10px; margin-bottom: 20px; flex-wrap: wrap; }
  
  /* Knapper og Piller */
  .filter-toggle-btn { background: #333; color: white; padding: 6px 14px; border: none; border-radius: 20px; cursor: pointer; font-size: 0.85em; display: flex; align-items: center; gap: 8px; }
  .counter-pill { background: #f8f8f8; color: #666; padding: 6px 14px; border-radius: 20px; font-size: 0.8em; border: 1px solid #ddd; display: flex; align-items: center; gap: 8px; }

  /* Filter-boks */
  .filter-box { display: none; background: #fff; padding: 20px; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1); border: 1px solid #eee; margin-bottom: 30px; }
  .filter-box.active { display: block; }
  .filter-group-wrapper { display: grid; grid-template-columns: repeat(auto-fill, minmax(140px, 1fr)); gap: 20px; }
  .filter-group h4 { margin: 0 0 10px 0; font-size: 0.7em; text-transform: uppercase; color: #999; }
  .filter-options { display: flex; flex-wrap: wrap; gap: 5px; }
  .filter-pill { background: #f5f5f5; border: 1px solid #eee; padding: 3px 8px; border-radius: 12px; font-size: 0.75em; cursor: pointer; color: #555; }
  .filter-pill.active { background: #222; color: #fff; }

  /* Footer Links - Sorte nu */
  .filter-footer { margin-top: 20px; font-size: 0.8em; display: flex; align-items: center; gap: 10px; }
  .footer-separator { color: #ccc; }
  .reset-link { background: none; border: none; color: #000; text-decoration: underline; cursor: pointer; padding: 0; font-weight: normal; }
  .reset-link:hover { color: #333; }

  /* Grid Layout */
  .recipe-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 15px; }

  @media (min-width: 768px) {
    .recipe-grid { grid-template-columns: repeat(auto-fill, 180px); gap: 20px; justify-content: flex-start; }
  }

  .recipe-teaser { display: flex; flex-direction: column; height: 100%; border-radius: 6px; overflow: hidden; text-decoration: none; color: inherit; border: 1px solid #eee; background: #fff; transition: transform 0.2s; }
  .recipe-teaser:hover { transform: translateY(-3px); box-shadow: 0 4px 10px rgba(0,0,0,0.05); }
  .recipe-img-container { width: 100%; height: 120px; overflow: hidden; background: #f9f9f9; }
  @media (min-width: 768px) { .recipe-img-container { height: 140px; } }
  .recipe-img-container img { width: 100%; height: 100%; object-fit: cover; }
  .recipe-info { padding: 8px; flex-grow: 1; text-align: center; }
  .recipe-info h3 { margin: 0; font-size: 0.85em; line-height: 1.2; color: #333; font-weight: 600; }
</style>
