---
layout: home
title: Who Is My Neta
---

<style>


  #tooltip {
    position: absolute;
    padding: 4px 8px;
    background: #333;
    color: #fff;
    border-radius: 4px;
    font-size: 12px;
    pointer-events: none;
    display: none;
  }

  #layout {
    display: flex;
    flex-wrap: wrap;
  }

  #map-container, #constituency-detail {
    width: 50%;
    padding: 1rem;
    box-sizing: border-box;
  }

  #map-container svg {
    width: 100%;
    height: auto;
    display: block;
  }

  #map-container path:hover {
  fill: #87ceeb !important;
}

</style>


<div id="layout">
  <div id="map-container"></div>

  <div id="constituency-detail">
    <h2>Click a constituency</h2>
    <label for="election-select" style="display: none;">Select election:</label>
    <select id="election-select" style="display: none;"></select>
    <div id="constituency-content"></div>
  </div>
</div>

<div id="tooltip"></div>

<script>
  const candidates = {{ site.data.all_candidates_national_elections_bangladesh | jsonify }};
  const tooltip = document.getElementById("tooltip");
  const contentDiv = document.getElementById("constituency-content");
  const select = document.getElementById("election-select");
  const label = document.querySelector("label[for='election-select']");
  let currentConstituency = null;
  let electionOptions = [];

  function updateContent() {
    const selectedElection = select.value;
    if (!currentConstituency || !selectedElection) return;

    const filtered = candidates.filter(c =>
      c.Constituency.toLowerCase() === currentConstituency &&
      c.election === selectedElection
    );

    contentDiv.innerHTML = filtered.length
      ? `<ul>${filtered.map(c => `<li><a href="/candidate/${c.ID}/">${c.Name}</a> (${c["Political Party"]})</li>`).join("")}</ul>`
      : "<p>No candidates found.</p>";
  }

  fetch('GRED_20190215_Bangladesh/bd_constituencies_shapefile/bangladesh_constituencies.svg')
    .then(res => res.text())
    .then(svg => {
      document.getElementById("map-container").innerHTML = svg;

      const allPaths = document.querySelectorAll('#map-container path');

      allPaths.forEach(path => {
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
          currentConstituency = seatId.toLowerCase();

          const related = candidates.filter(c =>
            c.Constituency.toLowerCase() === currentConstituency
          );

          const elections = {};
          related.forEach(c => {
            elections[c.election] = parseInt(c.Order) || 0;
          });

          electionOptions = Object.entries(elections)
            .sort((a, b) => b[1] - a[1])
            .map(e => e[0]);

          select.innerHTML = electionOptions.map(e => `<option value="${e}">${e}</option>`).join("");
          select.style.display = 'inline-block';
          label.style.display = 'inline-block';

          updateContent();
        });
      });

      select.addEventListener('change', updateContent);

      // ✅ Auto-select a random constituency on load
      const allConstituencies = [...new Set(candidates.map(c => c.Constituency.toLowerCase()))];
      const randomConstituency = allConstituencies[Math.floor(Math.random() * allConstituencies.length)];
      const randomPath = document.querySelector(`#map-container path[id="${randomConstituency}"]`);
      if (randomPath) randomPath.dispatchEvent(new Event('click'));
    });
</script>
