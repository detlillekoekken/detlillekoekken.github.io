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
      </div>
    </div>

    <div class="filter-group">
      <h4>Indhold</h4>
      <div class="filter-options">
        <span class="filter-pill" data-type="indhold" onclick="togglePill(this)">Svinekød</span>
        <span class="filter-pill" data-type="indhold" onclick="togglePill(this)">Oksekød</span>
        <span class="filter-pill" data-type="indhold" onclick="togglePill(this)">Vegansk</span>
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
         data-kategori="{{ opskrift.kategori }}" 
         data-metode="{{ opskrift.metode }}" 
         data-indhold="{{ opskrift.indhold }}"
         data-sværhedsgrad="{{ opskrift.sværhedsgrad }}">
      <a href="{{ opskrift.url | relative_url }}" class="recipe-teaser">
        {% if opskrift.recipe_image %}
          <img src="{{ opskrift.recipe_image | relative_url }}" alt="{{ opskrift.title }}">
        {% else %}
          <img src="{{ '/assets/images/standardmad.png' | relative_url }}" alt="Standard billede">
        {% endif %}
        <div class="recipe-info">
          <h3>{{ opskrift.title }}</h3>
          <p class="recipe-tags">{{ opskrift.kategori }} • {{ opskrift.metode }}</p>
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
  
  // Organiser valgte filtre i grupper
  const filters = {
    kategori: [],
    metode: [],
    indhold: [],
    sværhedsgrad: []
  };

  activePills.forEach(pill => {
    filters[pill.dataset.type].push(pill.textContent.trim());
  });

  const recipes = document.querySelectorAll('.recipe-item');
  let visibleCount = 0;

  recipes.forEach(recipe => {
    const rData = {
      kategori: recipe.getAttribute('data-kategori'),
      metode: recipe.getAttribute('data-metode'),
      indhold: recipe.getAttribute('data-indhold'),
      sværhedsgrad: recipe.getAttribute('data-sværhedsgrad')
    };

    // Logik: Hvis en gruppe er tom, er den altid "true". 
    // Hvis der er valgt noget, skal opskriften matche mindst én af de valgte i gruppen.
    const matchKategori = filters.kategori.length === 0 || filters.kategori.includes(rData.kategori);
    const matchMetode = filters.metode.length === 0 || filters.metode.includes(rData.metode);
    const matchIndhold = filters.indhold.length === 0 || filters.indhold.includes(rData.indhold);
    const matchSvær = filters.sværhedsgrad.length === 0 || filters.sværhedsgrad.includes(rData.sværhedsgrad);

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
  /* Filter UI */
  .filter-toggle-btn {
    background: #333;
    color: white;
    padding: 10px 20px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    font-size: 1em;
    margin-bottom: 20px;
  }
  .filter-box {
    display: none;
    background: #f8f9fa;
    padding: 25px;
    border-radius: 12px;
    border: 1px solid #eee;
    margin-bottom: 30px;
  }
  .filter-box.active { display: block; }
  
  .filter-group-wrapper {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
    gap: 30px;
  }
  .filter-group h4 {
    margin: 0 0 15px 0;
    font-size: 0.9em;
    text-transform: uppercase;
    letter-spacing: 1px;
    color: #666;
  }
  .filter-options { display: flex; flex-wrap: wrap; gap: 8px; }

  /* Pill Buttons */
  .filter-pill {
    background: white;
    border: 1px solid #ddd;
    padding: 6px 14px;
    border-radius: 20px;
    font-size: 0.85em;
    cursor: pointer;
    transition: all 0.2s ease;
    user-select: none;
  }
  .filter-pill:hover { border-color: #999; }
  .filter-pill.active {
    background: #333;
    color: white;
    border-color: #333;
  }

  .reset-link {
    background: none;
    border: none;
    color: #c0392b;
    text-decoration: underline;
    cursor: pointer;
    margin-top: 20px;
    padding: 0;
  }

  /* Grid layout */
  .recipe-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(260px, 1fr)); gap: 25px; }
  .recipe-teaser {
    display: block;
    border: 1px solid #eee;
    border-radius: 12px;
    overflow: hidden;
    text-decoration: none;
    color: inherit;
    transition: all 0.3s ease;
  }
  .recipe-teaser:hover { transform: translateY(-5px); box-shadow: 0 10px 20px rgba(0,0,0,0.1); }
  .recipe-teaser img { width: 100%; height: 180px; object-fit: cover; }
  .recipe-info { padding: 15px; }
  .recipe-info h3 { margin: 0; font-size: 1.1em; }
  .recipe-tags { font-size: 0.8em; color: #888; margin-top: 5px; }
</style>
