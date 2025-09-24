<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="description" content="Buscador de artículos científicos para enfermería y salud">
  <title>Buscador Científico</title>
  <style>
    body {
      font-family: "Times New Roman", serif;
      background-color: #fff;
      color: #000;
      margin: 0;
      padding: 0;
    }
    header {
      background: #000;
      color: #fff;
      text-align: center;
      padding: 1rem;
    }
    main {
      padding: 1rem;
      max-width: 900px;
      margin: auto;
    }
    form {
      display: flex;
      margin-bottom: 1rem;
    }
    input[type="text"] {
      flex: 1;
      padding: 0.5rem;
      border: 1px solid #000;
      font-family: "Times New Roman", serif;
      font-size: 1rem;
    }
    button {
      padding: 0.5rem 1rem;
      border: none;
      background: #000;
      color: #fff;
      font-size: 1rem;
      cursor: pointer;
    }
    .results {
      display: grid;
      grid-template-columns: 1fr;
      gap: 1rem;
    }
    .card {
      border: 1px solid #000;
      padding: 1rem;
      border-radius: 8px;
      background: #f9f9f9;
    }
    .card h3 {
      margin: 0 0 0.5rem 0;
    }
    .card p {
      margin: 0.25rem 0;
    }
    .footer {
      text-align: center;
      margin-top: 2rem;
      padding: 1rem;
      font-size: 0.9rem;
      color: #555;
    }
  </style>
</head>
<body>
  <header>
    <h1>Buscador de Artículos Científicos</h1>
    <p>PubMed · arXiv · CrossRef · Semantic Scholar</p>
  </header>

  <main>
    <form id="searchForm">
      <input type="text" id="query" placeholder="Introduce un tema (ej: úlceras por presión)" required>
      <button type="submit">Buscar</button>
    </form>

    <section class="results" id="results"></section>
  </main>

  <div class="footer">
    © 2025 Buscador Científico · Proyecto académico
  </div>

  <script>
    const form = document.getElementById('searchForm');
    const resultsContainer = document.getElementById('results');

    form.addEventListener('submit', async (e) => {
      e.preventDefault();
      const query = document.getElementById('query').value.trim();
      if (!query) return;

      resultsContainer.innerHTML = "<p>Buscando artículos...</p>";

      try {
        const response = await fetch(`http://127.0.0.1:5000/search?q=${encodeURIComponent(query)}`);
        if (!response.ok) throw new Error("Error en la búsqueda");
        const data = await response.json();

        if (data.length === 0) {
          resultsContainer.innerHTML = "<p>No se encontraron artículos.</p>";
          return;
        }

        resultsContainer.innerHTML = "";
        data.forEach(item => {
          const card = document.createElement('div');
          card.className = "card";
          card.innerHTML = `
            <h3>${item.title}</h3>
            <p><strong>Fuente:</strong> ${item.fuente}</p>
            <p><strong>Autores:</strong> ${item.authors.join(", ") || "No disponibles"}</p>
            <p><strong>DOI/Enlace:</strong> <a href="${item.doi}" target="_blank">${item.doi}</a></p>
            <p><strong>Resumen (original):</strong> ${item.abstract || "No disponible"}</p>
            <p><strong>Resumen en español:</strong> ${item.abstract_es || "No disponible"}</p>
          `;
          resultsContainer.appendChild(card);
        });
      } catch (err) {
        resultsContainer.innerHTML = `<p style="color:red;">Error: ${err.message}</p>`;
      }
    });
  </script>
</body>
</html>
