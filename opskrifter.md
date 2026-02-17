---
layout: single
title: "Find en opskrift"
permalink: /opskrifter/
---

<button class="btn btn--info" onclick="toggleFilter()" style="margin-bottom: 20px;">
  <i class="fas fa-filter"></i> Filtrér opskrifter
</button>

<div id="filter-container" style="display: none; background: #f4f4f4; padding: 20px; border-radius: 8px; margin-bottom: 30px;">
  <div class="filter-grid">
    
    <div class="filter-col">
      <h4>Kategori</h4>
      <label><input type="checkbox" class="filter-check" data-type="kategori" value="Forret"> Forret</label><br>
      <label><input type="checkbox" class="filter-check" data-type="kategori" value="Hovedret"> Hovedret</label><br>
      <label><input type="checkbox" class="filter-check" data-type="kategori" value="Dessert"> Dessert</label><br>
      <label><input type="checkbox" class="filter-check" data-type="kategori" value="Suppe"> Suppe</label>
    </div>

    <div class="filter-col">
      <h4>Metode</h4>
      <label><input type="checkbox" class="filter-check" data-type="metode" value="Sous Vide"> Sous Vide</label><br>
      <label><input type="checkbox" class="filter-check" data-type="metode" value="Grill"> Grill</label><br>
      <label><input type="checkbox" class="filter-check" data-type="metode" value="Trykkoger"> Trykkoger</label>
    </div>

    <div class="filter-col">
      <h4>Indhold</h4>
      <label><input type="checkbox" class="filter-check" data-type="indhold" value="Svinekød"> Svinekød</label><br>
      <label><input type="checkbox" class="filter-check" data-type="indhold" value="Oksekød"> Oksekød</label><br>
      <label><input type="checkbox" class="filter-check" data-type="indhold" value="Vegansk"> Vegansk</label>
    </div>

  </div>
  <br>
  <button class="btn btn--small" onclick="resetFilters()">Nulstil alle</button>
</div>

<hr>

{% assign sorterede_opskrifter = site.opskrifter | sort: "title" %}

<div class="recipe-grid" id="recipe-list">
  {% for opskrift in sorterede_opskrifter %}
    <div class="recipe-item" 
         data-kategori="{{ opskrift.kategori }}" 
         data-metode="{{ opskrift.metode }}" 
         data-indhold="{{ opskrift.indhold }}">
      <a href="{{ opskrift.url | relative_url }}" class="recipe-teaser">
        {% if opskrift.recipe_image %}
          <img src="{{ opskrift.recipe_image | relative_url }}" alt="{{ opskrift.title }}">
        {% else %}
          <img src="{{ '/assets/images/standardmad.png' | relative_url }}" alt="Standard billede">
        {% endif %}
        <div class="recipe-info">
          <h3>{{ opskrift.title }}</h3>
          <p class="recipe-tags">{{ opskrift.kategori }} • {{ opskrift.indhold }}</p>
        </div>
      </a>
    </div>
  {% endfor %}
</div>

<p id="no-results" style="display:none; text-align: center; margin-top: 50px;">Ingen opskrifter matcher de valgte filtre.</p>

<script>
// Vis/skjul filter-boksen
function toggleFilter() {
  const container = document.getElementById('filter-container');
  container.style.display = container.style.display === 'none' ? 'block' : 'none';
}

// Lyt efter ændringer på alle tjekbokse
document.querySelectorAll('.filter-check').forEach(box => {
  box.addEventListener('change', filterRecipes);
});

function filterRecipes() {
  // Hent alle valgte værdier fordelt på typer
  const activeFilters = {
    kategori: Array.from(document.querySelectorAll('.filter-check[data-type="kategori"]:checked')).map(cb => cb.value),
    metode: Array.from(document.querySelectorAll('.filter-check[data-type="metode"]:checked')).map(cb => cb.value),
    indhold: Array.from(document.querySelectorAll('.filter-check[data-type="indhold"]:checked')).map(cb => cb.value)
  };

  const recipes = document.querySelectorAll('.recipe-item');
  let visibleCount = 0;

  recipes.forEach(recipe => {
    const rKategori = recipe.getAttribute('data-kategori');
    const rMetode = recipe.getAttribute('data-metode');
    const rIndhold = recipe.getAttribute('data-indhold');

    // Tjek om opskriften passer i HVER kategori (hvis der er valgt noget i den kategori)
    const matchKategori = activeFilters.kategori.length === 0 || activeFilters.kategori.includes(rKategori);
    const matchMetode = activeFilters.metode.length === 0 || activeFilters.metode.includes(rMetode);
    const matchIndhold = activeFilters.indhold.length === 0 || activeFilters.indhold.includes(rIndhold);

    if (matchKategori && matchMetode && matchIndhold) {
      recipe.style.display = 'block';
      visibleCount++;
    } else {
      recipe.style.display = 'none';
    }
  });

  document.getElementById('no-results').style.display = visibleCount === 0 ? 'block' : 'none';
}

function resetFilters() {
  document.querySelectorAll('.filter-check').forEach(cb => cb.checked = false);
  filterRecipes();
}
</script>

<style>
  .filter-grid { display: flex; flex-wrap: wrap; gap: 40px; }
  .filter-col h4 { margin-top: 0; margin-bottom: 10px; border-bottom: 1px solid #ccc; }
  .filter-col label { cursor: pointer; display: inline-block; padding: 2px 0; }
  
  .recipe-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(240px, 1fr)); gap: 20px; }
  .recipe-teaser { display: block; border: 1px solid #eee; border-radius: 8px; overflow: hidden; text-decoration: none; color: inherit; transition: transform 0.2s; height: 100%; }
  .recipe-teaser img { width: 100%; height: 180px; object-fit: cover; }
  .recipe-info { padding: 15px; }
  .recipe-info h3 { margin: 0; font-size: 1.1em; color: #333; }
  .recipe-tags { font-size: 0.85em; color: #777; margin-top: 5px; }
</style>
