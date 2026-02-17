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
    <div class="recipe-item" 
         data-kategori="{{ opskrift.kategori | strip | downcase }}" 
         data-metode="{{ opskrift.metode | strip | downcase }}" 
         data-indhold="{{ opskrift.indhold | strip | downcase }}"
         data-sværhedsgrad="{{ opskrift.sværhedsgrad | strip | downcase }}">
      <a href="{{ opskrift.url | relative_url }}" class="recipe-teaser">
        <div class="recipe-img-container">
          {% if opskrift.recipe_image %}
            <img src="{{ opskrift.recipe_image | relative_url }}" alt="{{ opskrift.title }}">
          {% else %}
            <img src="{{ '/assets/images/standardmad.png' | relative_url }}" alt="Standard billede">
          {% endif %}
        </div>
        <div class="recipe-info">
          <h3>{{ opskrift.title }}</h3>
          <p class="recipe-tags">
            {{ opskrift.kategori }} {% if opskrift.sværhedsgrad %} • {{ opskrift.sværhedsgrad }}{% endif %}
          </p>
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
  
  const filters = {
    kategori: [],
    metode: [],
    indhold: [],
    sværhedsgrad: []
  };

  activePills.forEach(pill => {
    // Normaliserer teksten på knappen til sammenligning
    filters[pill.dataset.type].push(pill.textContent.trim().toLowerCase());
  });

  const recipes = document.querySelectorAll('.recipe-item');
  let visibleCount = 0;

  recipes.forEach(recipe => {
    // Vi henter data-attributterne som vi har pre-set til downcase i Liquid
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
  .filter-toggle-btn {
    background: #333; color: white; padding: 12px 24px; border: none; border-radius: 30px;
    cursor: pointer; font-size: 1em; margin-bottom: 20px;
  }
  .filter-box {
    display: none; background: #fff; padding: 25px; border-radius: 12px;
    box-shadow: 0 4px 12px rgba(0,0,0,0.08); border: 1px solid #eee; margin-bottom: 30px;
  }
  .filter-box.active { display: block; }
  .filter-group-wrapper { display: grid; grid-template-columns: repeat(auto-fill, minmax(180px, 1fr)); gap: 25px; }
  .filter-group h4 { margin: 0 0 12px 0; font-size: 0.8em;
