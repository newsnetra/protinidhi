---
layout: home
title: Who Is My Neta
---

<input type="text" id="search" placeholder="Search candidates...">

<ul id="candidate-list">
  {% assign candidates = site.data.all_candidates_national_elections_bangladesh %}
  {% for c in candidates %}
    <li data-name="{{ c.Name | downcase }}">
      <a href="/candidate/{{ c.ID }}/">{{ c.Name }} ({{ c.Seat }})</a>
    </li>
  {% endfor %}
</ul>

<script>
  const searchInput = document.getElementById('search');
  const items = document.querySelectorAll('#candidate-list li');
  searchInput.addEventListener('input', () => {
    const q = searchInput.value.toLowerCase();
    items.forEach(li => {
      li.style.display = li.dataset.name.includes(q) ? '' : 'none';
    });
  });
</script>
