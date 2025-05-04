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
</style>

<div style="display: flex; gap: 2rem;">
  <div id="map-container" style="flex: 1;"></div>

  <div id="constituency-detail" style="flex: 1;">
    <h2>Click a constituency</h2>
    <div id="constituency-content"></div>
  </div>
</div>

<div id="tooltip"></div>

<script>
  const candidates = {{ site.data.all_candidates_national_elections_bangladesh | jsonify }};
  const tooltip = document.getElementById("tooltip");

  fetch('GRED_20190215_Bangladesh/bd_constituencies_shapefile/bangladesh_constituencies.svg')
    .then(res => res.text())
    .then(svg => {
      document.getElementById("map-container").innerHTML = svg;

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
          const id = path.id.toLowerCase(); // e.g. "netrokona-2"
          const filtered = candidates.filter(c =>
            c.Constituency.toLowerCase() === id
          );

          const target = document.getElementById("constituency-content");

          if (filtered.length) {
            const grouped = {};
            filtered.forEach(c => {
              if (!grouped[c.election]) grouped[c.election] = [];
              grouped[c.election].push(c);
            });

            target.innerHTML = Object.keys(grouped).map(election => `
              <h3>${election}</h3>
              <ul>
                ${grouped[election].map(c => `
                  <li><a href="/candidate/${c.ID}/">${c.Name}</a> (${c["Political Party"]})</li>
                `).join("")}
              </ul>
            `).join("");
          } else {
            target.innerHTML = `<p>No data for ${id}</p>`;
          }
        });
      });
    });
</script>
