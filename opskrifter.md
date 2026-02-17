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
    
    <div class="filter-group">
      <h4>Kategori</h4>
      <div class="filter-options">
        <span class="filter-pill" data-type="kategori" onclick="togglePill(this)">Forret</span>
        <span class="filter-pill" data-type="kategori" onclick="togglePill(this)">Hovedret</span>
        <span class="filter-pill" data-type="kategori" onclick="togglePill(this)">Dessert</span>
        <span class="filter-pill" data-type="kategori" onclick="togglePill(this)">Suppe</span>
        <span class="filter-pill" data-type="kategori" onclick="togglePill(this)">Snack</span>
      </div>
    </div>

    <div class="filter-group">
      <h4>Metode</h4>
      <div class="filter-options">
        <span class="filter-pill" data-type="metode" onclick="togglePill(this)">Sous Vide</span>
        <span class="filter-pill" data-type="metode" onclick="togglePill(this)">Grill</span>
        <span class="filter-pill" data-type="metode" onclick="togglePill(this)">Røgning</span>
        <span class="filter-pill" data-type="metode" onclick="togglePill(this)">Trykkoger</span>
        <span class="filter-pill" data-type="metode" onclick="togglePill(this)">Slow Cooker</span>
        <span class="filter-pill" data-type="metode" onclick="togglePill(this)">Airfryer</span>
        <span class="filter-pill" data-type="metode" onclick="togglePill(this)">Ovn</span>
      </div>
    </div>

    <div class="filter-group">
      <h4>Indhold</h4>
      <div class="filter-options">
        <span class="filter-pill" data-type="indhold" onclick="togglePill(this)">Svinekød</span>
        <span class="filter-pill" data-type="indhold" onclick="togglePill(this)">Oksekød</span>
        <span class="filter-pill" data-type="indhold" onclick="togglePill(this)">Vegansk</span>
        <span class="filter-pill" data-type="indhold" onclick="togglePill(this)">Fisk</span>
        <span class="filter-pill" data-type="indhold" onclick="togglePill(this)">Vegetarisk</span>
      </div>
    </div>

    <div class="filter-group">
      <h4>Sværhedsgrad</h4>
      <div class="filter-options">
        <span class="filter-pill" data-type="sværhedsgrad" onclick="togglePill(this)">Nem</span>
        <span class="filter-pill" data-type="sværhedsgrad" onclick="togglePill(this)">Middel</span>
        <span class="filter-pill" data-type="sværhedsgrad" onclick="togglePill(this)">Svær</span>
      </div>
    </div>

  </div>
  <button class="reset-link" onclick="resetFilters()">Nulstil alle filtre</button>
</div>

<hr>

{% assign sorterede_opskrifter = site.opskrifter | sort: "title" %}

<div class="recipe-grid" id="recipe-list">
  {% for opskrift in sorterede_opskrifter %}
    {% comment %} Vi tjekker begge stavemåder for at være sikre {% endcomment %}
    {% assign s_grad = opskrift.sværhedsgrad | default: opskrift.svaerhedsgrad | strip | downcase %}
    
    <div class="recipe-item" 
         data-kategori="{{ opskrift.kategori | strip | downcase }}" 
         data-metode="{{ opskrift.metode | strip | downcase }}" 
         data-indhold="{{ opskrift.indhold | strip | downcase }}"
         data-sværhedsgrad="{{ s_grad }}">
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
          <p class="recipe-tags">{{ opskrift.kategori | capitalize }} • {{ s_grad | capitalize }}</p>
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
  const filters = { kategori: [], metode: [], indhold: [], sværhedsgrad: [] };

  activePills.forEach(pill => {
    filters[pill.dataset.type].push(pill.textContent.trim().toLowerCase());
  });

  const recipes = document.querySelectorAll('.recipe-item');
  let visibleCount = 0;

  recipes.forEach(recipe => {
    const rKategori = recipe.getAttribute('data-kategori') || "";
    const rMetode = recipe.getAttribute('data-metode') || "";
    const rIndhold = recipe.getAttribute('data-indhold') || "";
    const rSvær = recipe.getAttribute('data-sværhedsgrad') || "";

    const matchKategori = filters.kategori.length === 0 || filters.kategori.includes(rKategori);
    const matchMetode = filters.metode.length === 0 || filters.metode.includes(rMetode);
    const matchIndhold = filters.indhold.length === 0 || filters.indhold.includes(rIndhold);
    const matchSvær = filters.sværhedsgrad.length === 0 || filters.sværhedsgrad.includes(rSvær);

    if (matchKategori && matchMetode && matchIndhold && matchSvær) {
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
    font-size: 0.8em; cursor: pointer; transition: background 0.2s, color 0.2s; color: #555;
    white-space: nowrap;
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
