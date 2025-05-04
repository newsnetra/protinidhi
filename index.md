---
layout: home
title: Who Is My Neta
---

<div id="map-container"></div>

<script>
  fetch('/assets/svg/bangladesh_constituencies.svg')
    .then(res => res.text())
    .then(svg => {
      document.getElementById("map-container").innerHTML = svg;

      // Tooltip
      const tooltip = document.createElement("div");
      tooltip.style.position = "absolute";
      tooltip.style.padding = "4px 8px";
      tooltip.style.background = "#333";
      tooltip.style.color = "#fff";
      tooltip.style.borderRadius = "4px";
      tooltip.style.fontSize = "12px";
      tooltip.style.pointerEvents = "none";
      tooltip.style.display = "none";
      document.body.appendChild(tooltip);

      document.querySelectorAll('#map-container path').forEach(path => {
        const seatId = path.id;
        path.style.cursor = 'pointer';

        path.addEventListener('mousemove', (e) => {
          tooltip.style.left = (e.pageX + 10) + "px";
          tooltip.style.top = (e.pageY + 10) + "px";
          tooltip.textContent = seatId.replace(/-/g, " ").toUpperCase();
          tooltip.style.display = "block";
        });

        path.addEventListener('mouseleave', () => {
          tooltip.style.display = "none";
        });

        path.addEventListener('click', () => {
          window.location.href = "/constituency/" + seatId + "/";
        });
      });
    });
</script>

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

  renderList([]);

  // 🔍 Text Search
  searchInput.addEventListener('input', () => {
    const q = searchInput.value.toLowerCase();
    const filtered = candidates.filter(c => c.Name.toLowerCase().includes(q));
    renderList(filtered);
  });
</script>
