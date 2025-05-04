---
layout: home
title: Who Is My Neta
---

<input type="text" id="search" placeholder="Search candidates...">

<ul id="candidate-list">
  {% assign candidates = site.candidates | sort: "name" %}
  {% for candidate in candidates %}
    <li data-name="{{ candidate.name | downcase }}">
      <a href="{{ candidate.url }}">{{ candidate.name }} ({{ candidate.constituency }})</a>
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
