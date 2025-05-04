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

.winner-party {
  background-color: rgb(255, 153, 51);
    color: rgb(255, 255, 255);
    font-weight: 700;
    font-size: .875rem;
    line-height: 1.25rem;
    font-family: ui-monospace;
        padding-left: .2rem;
    padding-right: .2rem;
    border-radius: .375rem;
    display: inline-flex;
    margin-bottom: .2rem;
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

  <div id="other-candidates" style="padding: 1rem;"></div>
</div>

<div id="tooltip"></div>

<script>
  const candidates = {{ site.data.all_candidates_national_elections_bangladesh | jsonify }};
  const tooltip = document.getElementById("tooltip");
  const contentDiv = document.getElementById("constituency-content");
  const select = document.getElementById("election-select");
  const label = document.querySelector("label[for='election-select']");
  const othersDiv = document.getElementById("other-candidates");
  let currentConstituency = null;
  let electionOptions = [];

  function updateContent() {
    if (!currentConstituency || !select.value) return;

    const selectedElection = select.value;
    const seatName = currentConstituency.replace(/-/g, ' ').toUpperCase();
    contentDiv.innerHTML = `<h2>${seatName}</h2>`;

    const filtered = candidates.filter(c =>
      c.Constituency.toLowerCase() === currentConstituency &&
      c.election === selectedElection
    );

    if (filtered.length === 0) {
      contentDiv.innerHTML += "<p>No candidates found.</p>";
      othersDiv.innerHTML = "";
      return;
    }

    const winners = filtered.find(c => {
      const val = (c.Winners ?? '').toString().trim().toLowerCase();
      console.log("Checking:", val, c.Name);
      return val === 'yes';
    });

    console.log("Winners values:", filtered.map(c => c.Winners));

    if (winners) {
     contentDiv.innerHTML += `
  <div class="winner-block">
    <p class="winner-party"><strong>Party:</strong> ${winners["Political Party"]}</p>
    <h3 class="winner-name">Winner: ${winners.Name}</h3>
    <p class="winner-father"><strong>Father:</strong> ${winners["Father Name"]}</p>
    <p class="winner-mother"><strong>Mother:</strong> ${winners["Mother Name"]}</p>
    <p class="winner-profession"><strong>Profession:</strong> ${winners["Profession"]}</p>
    <p class="winner-address"><strong>Address:</strong> ${winners["Address"]}</p>
    <p>
      <a href="/candidate/${winners.ID}/" target="_blank" class="learn-more-button">
        Learn More &#x2197;
      </a>
    </p>
  </div>
`;

     }

    const nonWinners = filtered.filter(c => c.ID !== (winners ? winners.ID : null));
    othersDiv.innerHTML = nonWinners.length
      ? `<h3>Other Candidates</h3><ul>${nonWinners.map(c => `
            <li><a href="/candidate/${c.ID}/">${c.Name}</a> (${c["Political Party"]})</li>`).join("")}</ul>`
      : "";
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
            elections[c.election] = parseInt(c.Order) || 99;
          });

          electionOptions = Object.entries(elections)
            .sort((a, b) => a[1] - b[1])
            .map(e => e[0]);

          select.innerHTML = electionOptions.map(e => `<option value="${e}">${e}</option>`).join("");
          select.value = electionOptions[0];
          select.style.display = 'inline-block';
          label.style.display = 'inline-block';

          updateContent();
        });
      });

      select.addEventListener('change', updateContent);

      // Auto-select a random constituency on load
      const allConstituencies = [...new Set(candidates.map(c => c.Constituency.toLowerCase()))];
      const randomConstituency = allConstituencies[Math.floor(Math.random() * allConstituencies.length)];
      const randomPath = document.querySelector(`#map-container path[id="${randomConstituency}"]`);

      if (randomPath) {
        const seatId = randomPath.id;
        currentConstituency = seatId.toLowerCase();

        const related = candidates.filter(c =>
          c.Constituency.toLowerCase() === currentConstituency
        );

        const elections = {};
        related.forEach(c => {
          elections[c.election] = parseInt(c.Order) || 99;
        });

        electionOptions = Object.entries(elections)
          .sort((a, b) => a[1] - b[1])
          .map(e => e[0]);

        select.innerHTML = electionOptions.map(e => `<option value="${e}">${e}</option>`).join("");
        select.value = electionOptions[0];
        select.style.display = 'inline-block';
        label.style.display = 'inline-block';

        updateContent();
      }
    });
</script>
