---
layout: default
title: Protinidhi
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
#map-container {
  min-height: 400px; /* ensures the container isn't empty */
}

  #map-container, #constituency-detail {
    width: 40%;
    padding: 1rem;
    box-sizing: border-box;
    min-height: 400px;
    margin: auto;
  }

  #constituency-detail {
background-color: #F2F1EE;
    padding: 1rem 1.5rem;
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
    color: rgb(255, 255, 255);
    font-weight: 700;
    font-size: .875rem;
    line-height: 1.25rem;
    border-radius: .375rem;
    display: inline-flex;
    margin-bottom: .2rem;
    padding: 0.2rem 0.4rem;
}


/* Party-specific overrides */
.party-bnp { background: #004488; color: #fff;}
.party-awami-league { background: #006600; color: #fff; }
.party-jamaat { background: #660066; color: #fff; }
.party-jatiya-party { background: #990000; color: #fff; }
.party-independent { background: #666; color: #fff; }
.party-jasad { background: #003366; color: #fff; }
.party-workers-party { background: #a80000; color: #fff; }
.party-jp { background: #cc6600; color: #fff; }
.party-bnf { background: #009999; color: #fff; }
.party-tarikat-fedaration { background: #006680; color: #fff; }
.party-ldp { background: #990099; color: #fff; }
.party-bikalpa-dhara { background: #336699; color: #fff; }
.party-gono-forum { background: #5555aa; color: #fff; }
.party-kalyan-party { background: #884400; color: #fff; }

.winner-name {
font-weight: 500;
    font-size: 2rem;
    line-height: 1.5rem;
    text-wrap: pretty;
    width: -moz-fit-content;
    width: fit-content;
    font-family: "Playfair Display", serif;
}


#other-candidates {
    width: 100%;
    box-sizing: border-box;
}




#other-candidates li a {
 color: gray;
    text-decoration: none;
    background-color: #F6F1EF;
    font-size: .75rem;
    padding: .25rem .5rem;
}

/* Style the learn-more button on hover */
#other-candidates li a:hover {
  text-decoration: none;
  background-color: #BC8585;  /* darker than #F6F1EF */
  color: #ffffff;
}

/* Base style for party icon */
#other-candidates .party-icon {
  width: 40px;
  height: 40px;
  object-fit: cover;
  transition: border-radius 0.2s, border 0.2s;
}

/* Add circular border on li hover */
#other-candidates li:hover .party-icon {
  border-radius: 50%;
  border: 2px solid #ccc;
}

.nonwinner-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 0.75rem;
  list-style: none;
  padding: 0;
  margin: 0.5rem 0;
}
.nonwinner-card {
  padding: 0.75rem;
  background: #f9f9f9;
  border-radius: 6px;
  box-shadow: 0 1px 2px rgba(0,0,0,0.05);
  display: flex;
  align-items: flex-start;
}

.nonwinner-card-inner {
  display: flex;
  gap: 0.75rem;
}


.nonwinner-info {
  flex-grow: 1;
}

.nonwinner-name {
  font-weight: 700;
  font-size: 1rem;
  margin-bottom: 0.2rem;
}

.nonwinner-party {
  font-size: 0.9rem;
  color: #555;
  margin-bottom: 0.4rem;
}


.learn-more-button {
 color: #fff;
    background-color: #BC8585;
    letter-spacing: .05em;
    font-weight: 500;
    font-size: .875rem;
    line-height: 1.25rem;
    padding: .5rem 1rem;
}

#election-select {
  appearance: none; /* Removes native styling */
  -webkit-appearance: none;
  -moz-appearance: none;
  background-color: #F0EFEB;
  border: 1px solid #6C6B66;
  padding: 0.5em 2.5em 0.5em 1em;
  font-size: 1rem;
  font-family: inherit;
  color: #333;
  border-radius: 6px;
  background-image: url('data:image/svg+xml;charset=US-ASCII,<svg xmlns="http://www.w3.org/2000/svg" width="12" height="8" viewBox="0 0 12 8"><path fill="%236C6B66" d="M6 8L0 0h12z"/></svg>');
  background-repeat: no-repeat;
  background-position: right 1em center;
  background-size: 0.65em auto;
  cursor: pointer;
  transition: border-color 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
}

#election-select:focus {
  outline: none;
  border-color: #BC8585;
  box-shadow: 0 0 0 2px rgba(188, 133, 133, 0.3);
}

@media (max-width: 541px) {
 
  #map-container,
  #constituency-detail {
    width: 100%;
    display: block;
  }

  #layout {
    flex-direction: column;
  }
}

</style>

<div id="layout">
  <div id="map-container"></div>

  <div id="constituency-detail">
    <!-- <h2>Click a constituency</h2> -->
    <label for="election-select" style="display: none;"></label>
    <select id="election-select" style="display: none;"></select>
    <div id="constituency-content"></div>
  </div>

  <div id="other-candidates" style="padding: 1rem;"></div>
</div>

<div id="tooltip"></div>


<script>
  document.addEventListener("DOMContentLoaded", function() {
    console.log("✅ DOM fully loaded and parsed.");

    const candidates = {{ site.data.all_candidates_national_elections_bangladesh | jsonify }};
    console.log("📦 Candidates data loaded:", candidates.length);

    const tooltip = document.getElementById("tooltip");
    const contentDiv = document.getElementById("constituency-content");
    const select = document.getElementById("election-select");
    const label = document.querySelector("label[for='election-select']");
    const othersDiv = document.getElementById("other-candidates");

    let currentConstituency = null;
    let electionOptions = [];

function updateContent() {
  console.log("🔁 updateContent called");

  if (!currentConstituency || !select.value) {
    console.log("⛔ No constituency or election selected.");
    return;
  }

  console.log("🗳 Selected election:", select.value);

  const selectedElection = select.value;
  const seatName = currentConstituency.replace(/-/g, ' ').toUpperCase();
  contentDiv.innerHTML = `<h2>${seatName}</h2>`;

  const filtered = candidates.filter(c =>
    c.Constituency.toLowerCase() === currentConstituency &&
    c.election === selectedElection
  );
  console.log("🔍 Filtered candidates:", filtered.length, filtered);

  if (filtered.length === 0) {
    contentDiv.innerHTML += "<p>No candidates found.</p>";
    othersDiv.innerHTML = "";
    return;
  }

  const winners = filtered.find(c => {
    const val = (c.Winners ?? '').toString().trim().toLowerCase();
    return val === 'yes';
  });

if (winners) {
  try {
    const voteSummary = filtered.find(c =>
      c["Total Votes"] && !isNaN(c["Total Votes"]) &&
      c["Valid Votes"] && !isNaN(c["Valid Votes"]) &&
      c["Winning Vote"] && !isNaN(c["Winning Vote"])
    );

    let voteBarHTML = '';
    if (voteSummary) {
      const total = parseInt(voteSummary["Total Votes"]);
      const valid = parseInt(voteSummary["Valid Votes"]);
      const winning = parseInt(voteSummary["Winning Vote"]);

      const validPct = Math.min(100, (valid / total) * 100);
      const winningPct = Math.min(100, (winning / total) * 100);

      voteBarHTML = `
  <div class="vote-bar-wrapper" style="margin: 1em 0;">
    <div style="background: #eee; height: 20px; width: 100%; border-radius: 4px; overflow: hidden; position: relative;">
      <div style="background: #4caf50; width: ${validPct}%; height: 100%;"></div>
      <div style="background: #2196f3; width: ${winningPct}%; height: 100%; position: absolute; top: 0; left: 0;"></div>
    </div>
    <div style="display: flex; justify-content: space-between; font-size: 0.9em; margin-top: 0.3em;">
      <span>Total Votes: ${total}</span>
      <span>Valid Votes: ${valid}</span>
      <span>Winning Vote: ${winning}</span>
    </div>
  </div>
`;

    }

    contentDiv.innerHTML += `
      ${voteBarHTML}
      <div class="winner-block">
        <p class="winner-party party-${(winners["Political Party"] || "independent").toLowerCase().replace(/\s+/g, '-')}">
          ${winners["Political Party"] || "N/A"}
        </p>
        <h3 class="winner-name">${winners.Name || "N/A"}</h3>
        <p class="winner-father"><strong>Father:</strong> ${winners["Father Name"] || ""}</p>
        <p class="winner-mother"><strong>Mother:</strong> ${winners["Mother Name"] || ""}</p>
        <p class="winner-profession"><strong>Profession:</strong> ${winners["Profession"] || ""}</p>
        <p class="winner-address"><strong>Address:</strong> ${winners["Address"] || ""}</p>
        <p>
          <a href="/candidate/${winners.ID}/" target="_blank" class="learn-more-button">
            Learn More &#x2197;
          </a>
        </p>
      </div>
    `;
  } catch (e) {
    console.error("❌ Error rendering winner block:", e);
  }
}



try {
  const nonWinners = filtered.filter(c => c.ID !== (winners ? winners.ID : null));
  console.log("👥 Non-winners to display:", nonWinners.length, nonWinners);

  othersDiv.innerHTML = nonWinners.length
    ? `<h3>Other Candidates</h3><ul class="nonwinner-grid">${nonWinners.map(c => {
        const gender = (c.Gender || '').toLowerCase().startsWith('m') ? 'M' : (c.Gender || '').toLowerCase().startsWith('f') ? 'F' : '';
        const age = c.Age && !isNaN(c.Age) ? `${c.Age}` : '';
        const party = c["Political Party"] || "Independent";

        const knownParties = [
          "Awami League", "Workers Party", "Jatiya Party", "BNP", "Jasad", "Tarikat Fedaration", "LDP",
          "Jamaat", "Kalyan Party", "BNF", "JP", "Bikalpa Dhara", "Gono Forum"
        ];
        const partyKey = knownParties.includes(party) ? party.toLowerCase().replace(/\s+/g, '-') : 'independent';
        const partyImgSrc = `/assets/party-symbols/${partyKey}.svg`;

        return `
          <li class="nonwinner-card">
            <div class="nonwinner-card-inner">
              <img src="${partyImgSrc}" alt="${party}" class="party-icon">
              <div class="nonwinner-info">
                <div class="nonwinner-name">${c.Name}${gender || age ? ` (${[gender, age].filter(Boolean).join(', ')})` : ''}</div>
                <div class="nonwinner-party">${party}</div>
                <a href="/candidate/${c.ID}/" target="_blank" class="learn-more-button">Learn More &#x2197;</a>
              </div>
            </div>
          </li>`;
      }).join("")}</ul>`
    : "";
} catch (e) {
  console.error("❌ Error rendering other candidates:", e);
}
}


    fetch('GRED_20190215_Bangladesh/bd_constituencies_shapefile/bangladesh_constituencies.svg')
      .then(res => {
        console.log("📡 Fetching SVG map...");
        return res.text();
      })
      .then(svg => {
        console.log("🗺️ SVG map loaded");
        document.getElementById("map-container").innerHTML = svg;

        const allPaths = document.querySelectorAll('#map-container path');
        console.log("🧩 Paths found:", allPaths.length);

        if (allPaths.length === 0) {
          console.error("❌ No <path> elements found in SVG.");
        }

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
            console.log("📍 Clicked constituency:", currentConstituency);

            const related = candidates.filter(c =>
              c.Constituency.toLowerCase() === currentConstituency
            );
            console.log("🧮 Related candidates for constituency:", related.length);

            const elections = {};
            related.forEach(c => {
              elections[c.election] = parseInt(c.Order) || 99;
            });

            electionOptions = Object.entries(elections)
              .sort((a, b) => a[1] - b[1])
              .map(e => e[0]);

            console.log("🗂️ Election options sorted by order:", electionOptions);

            select.innerHTML = electionOptions.map(e => `<option value="${e}">${e}</option>`).join("");
            select.value = electionOptions[0];
            select.style.display = 'inline-block';
            label.style.display = 'inline-block';

            updateContent();
          });
        });

        select.addEventListener('change', updateContent);

        // Auto-select a random constituency
        const allConstituencies = [...new Set(candidates.map(c => c.Constituency.toLowerCase()))];
        const randomConstituency = allConstituencies[Math.floor(Math.random() * allConstituencies.length)];
        console.log("🎯 Auto-selecting random constituency:", randomConstituency);

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

  console.log("🎯 Random constituency selected:", currentConstituency);
  console.log("🗳 Selected election:", select.value);
  updateContent(); // <- Call this **only after select.value is set**
}

      })
      .catch(error => {
        console.error("🚨 Error loading SVG map:", error);
      });
  });
</script>
