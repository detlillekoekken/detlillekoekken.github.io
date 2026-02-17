---
layout: single
title: "Alle Opskrifter"
permalink: /opskrifter/
---

<div class="filter-section">
  <div class="filter-group">
    <label for="filter-kategori">Kategori</label>
    <select id="filter-kategori" onchange="filterRecipes()">
      <option value="all">Alle</option>
      <option value="Forret">Forret</option>
      <option value="Hovedret">Hovedret</option>
      <option value="Dessert">Dessert</option>
      <option value="Suppe">Suppe</option>
      <option value="Snack">Snack</option>
      <option value="Slik">Slik</option>
    </select>
  </div>

  <div class="filter-group">
    <label for="filter-metode">Metode</label>
    <select id="filter-metode" onchange="filterRecipes()">
      <option value="all">Alle</option>
      <option value="Sous Vide">Sous Vide</option>
      <option value="Grill">Grill</option>
      <option value="Røgning">Røgning</option>
      <option value="Trykkoger">Trykkoger</option>
      <option value="Slow Cook">Slow Cook</option>
    </select>
  </div>

  <div class="filter-group">
    <label for="filter-indhold">Indhold</label>
    <select id="filter-indhold" onchange="filterRecipes()">
      <option value="all">Alt</option>
      <option value="Svinekød">Svinekød</option>
      <option value="Oksekød">Oksekød</option>
      <option value="Vegansk">Vegansk</option>
    </select>
  </div>

  <div class="filter-group">
    <label for="filter-sværhedsgrad">Sværhedsgrad</label>
    <select id="filter-sværhedsgrad" onchange="filterRecipes()">
      <option value="all">Alle</option>
      <option value="Nem">Nem</option>
      <option value="Middel">Middel</option>
      <option value="Svær">Svær</option>
      <option value="Ekspert">Ekspert</option>
    </select>
  </div>

  <button class="btn btn--inverse" onclick="resetFilters()" style="margin-top: 25px;">Nulstil</button>
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
          <p class="recipe-tags">{{ opskrift.kategori }} • {{ opskrift.sværhedsgrad }}</p>
        </div>
      </a>
    </div>
  {% endfor %}
</div>

<p id="no-results" style="display:none; text-align: center; margin-top: 50px;">Ingen opskrifter matcher de valgte filtre.</p>

<script>
function filterRecipes() {
  const kategori = document.getElementById('filter-kategori').value;
  const metode = document.getElementById('filter-metode').value;
  const indhold = document.getElementById('filter-indhold').value;
  const svær = document.getElementById('filter-sværhedsgrad').value;
  
  const recipes = document.querySelectorAll('.recipe-item');
  let count = 0;

  recipes.forEach(recipe => {
    // Vi tjekker om attributten findes og matcher, eller om 'all' er valgt
    const matchKategori = kategori === 'all' || recipe.getAttribute('data-kategori') === kategori;
    const matchMetode = metode === 'all' || recipe.getAttribute('data-metode') === metode;
    const matchIndhold = indhold === 'all' || recipe.getAttribute('data-indhold') === indhold;
    const matchSvær = svær === 'all' || recipe.getAttribute('data-sværhedsgrad') === svær;

    if (matchKategori && matchMetode && matchIndhold && matchSvær) {
      recipe.style.display = 'block';
      count++;
    } else {
      recipe.style.display = 'none';
    }
  });

  document.getElementById('no-results').style.display = count === 0 ? 'block' : 'none';
}

function resetFilters() {
  document.getElementById('filter-kategori').value = 'all';
  document.getElementById('filter-metode').value = 'all';
  document.getElementById('filter-indhold').value = 'all';
  document.getElementById('filter-sværhedsgrad').value = 'all';
  filterRecipes();
}
</script>

<style>
  .filter-section {
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
    background: #f4f4f4;
    padding: 20px;
    border-radius: 8px;
    margin-bottom: 30px;
  }
  .filter-group {
    display: flex;
    flex-direction: column;
    min-width: 140px;
  }
  .filter-group label {
    font-weight: bold;
    margin-bottom: 5px;
    font-size: 0.85em;
  }
  .recipe-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(240px, 1fr));
    gap: 20px;
  }
  .recipe-teaser {
    display: block;
    border: 1px solid #eee;
    border-radius: 8px;
    overflow: hidden;
    text-decoration: none;
    color: inherit;
    transition: transform 0.2s;
  }
  .recipe-teaser:hover {
    transform: translateY(-5px);
    box-shadow: 0 4px 15px rgba(0,0,0,0.1);
  }
  .recipe-teaser img {
    width: 100%;
    height: 180px;
    object-fit: cover;
  }
  .recipe-info {
    padding: 15px;
  }
  .recipe-info h3 {
    margin: 0;
    font-size: 1.1em;
  }
  .recipe-tags {
    font-size: 0.8em;
    color: #666;
    margin-top: 5px;
  }
</style>
