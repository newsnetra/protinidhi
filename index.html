<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>{{ site.title }}</title>
  <style>
    body {
      font-family: sans-serif;
      max-width: 600px;
      margin: 2rem auto;
      padding: 0 1rem;
    }
    h1 {
      text-align: center;
    }
    input {
      width: 100%;
      padding: 0.5rem;
      font-size: 1rem;
      margin-bottom: 1rem;
    }
    ul {
      list-style: none;
      padding: 0;
    }
    li {
      padding: 0.5rem 0;
      border-bottom: 1px solid #ddd;
    }
    a {
      text-decoration: none;
      color: #007acc;
    }
  </style>
</head>
<body>
  <h1>{{ site.title }}</h1>
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
</body>
</html>
