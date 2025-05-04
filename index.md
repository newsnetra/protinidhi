---
layout: home
title: Who Is My Neta
---

<div id="map-container">

</div>

<hr>

<input type="text" id="search" placeholder="Search candidates...">

<ul id="candidate-list"></ul>

<script>
  const candidates = {{ site.data.all_candidates_national_elections_bangladesh | jsonify }};
  const list = document.getElementById("candidate-list");
  const searchInput = document.getElementById("search");

  function renderList(filtered) {
    list.innerHTML = filtered.length
      ? filtered.map(c => `<li data-name="${c.Name.toLowerCase()}">
            <a href="/candidate/${c.ID}/">${c.Name} (${c.Seat})</a>
         </li>`).join("")
      : "<li>No candidates found</li>";
  }

  // Initial: show none
  renderList([]);

  // 🔍 Text Search
  searchInput.addEventListener('input', () => {
    const q = searchInput.value.toLowerCase();
    const filtered = candidates.filter(c => c.Name.toLowerCase().includes(q));
    renderList(filtered);
  });

  // 🗺️ Map Click
  document.querySelectorAll('#map-container path').forEach(path => {
    path.style.cursor = 'pointer';
    path.addEventListener('click', () => {
      const id = path.id.toLowerCase(); // e.g., "dhaka-8"
      const filtered = candidates.filter(c =>
        c.Seat.toLowerCase().replace(/\s+/g, "-") === id
      );
      searchInput.value = ""; // clear search box
      renderList(filtered);
    });
  });
</script>
