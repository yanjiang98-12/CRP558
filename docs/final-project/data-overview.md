---
title: "Final Project Data Overview"
permalink: /final-project/data-overview/
---

Final Project Data Overview - here you will list the data you use and the location or source of that data.

## Medicated Feed Mill License Dataset

---
title: "Final Project Data Overview"
permalink: /final-project/data-overview/
---

Final Project Data Overview - here you will list the data you use and the location or source of that data.

## Medicated Feed Mill License Dataset

<div style="margin-bottom: 1rem;">
  <input
    type="text"
    id="csv-search"
    placeholder="Search this table"
    style="width: 100%; max-width: 420px; padding: 8px; border: 1px solid #ccc; border-radius: 4px;"
  >
</div>

<div style="overflow-x: auto;">
  <table id="csv-table" style="width: 100%; border-collapse: collapse; border: 1px solid #ccc;">
    <thead></thead>
    <tbody></tbody>
  </table>
</div>

<script src="https://cdn.jsdelivr.net/npm/papaparse@5.4.1/papaparse.min.js"></script>
<script>
document.addEventListener("DOMContentLoaded", function () {
  const csvUrl = "{{ '/final-project/Medicated_Feed_Mill_Licenses_with_County.csv' | relative_url }}";
  const table = document.getElementById("csv-table");
  const thead = table.querySelector("thead");
  const tbody = table.querySelector("tbody");
  const searchInput = document.getElementById("csv-search");

  let allRows = [];
  let headers = [];

  function escapeHtml(value) {
    return String(value ?? "")
      .replace(/&/g, "&amp;")
      .replace(/</g, "&lt;")
      .replace(/>/g, "&gt;")
      .replace(/"/g, "&quot;")
      .replace(/'/g, "&#39;");
  }

  function renderTable(rows) {
    thead.innerHTML = "";
    tbody.innerHTML = "";

    if (!headers.length) return;

    const headerRow = document.createElement("tr");
    headers.forEach(header => {
      const th = document.createElement("th");
      th.textContent = header;
      th.style.border = "1px solid #ccc";
      th.style.padding = "8px";
      th.style.backgroundColor = "#f2f2f2";
      th.style.textAlign = "left";
      headerRow.appendChild(th);
    });
    thead.appendChild(headerRow);

    rows.forEach(row => {
      const tr = document.createElement("tr");
      headers.forEach(header => {
        const td = document.createElement("td");
        td.innerHTML = escapeHtml(row[header] || "");
        td.style.border = "1px solid #ccc";
        td.style.padding = "8px";
        tr.appendChild(td);
      });
      tbody.appendChild(tr);
    });
  }

  Papa.parse(csvUrl, {
    download: true,
    header: true,
    skipEmptyLines: true,
    complete: function(results) {
      allRows = results.data;
      headers = results.meta.fields || [];
      renderTable(allRows);
    },
    error: function(err) {
      tbody.innerHTML = '<tr><td style="padding:8px; color:red;">Failed to load CSV: ' + escapeHtml(err.message) + '</td></tr>';
    }
  });

  searchInput.addEventListener("input", function () {
    const keyword = this.value.trim().toLowerCase();

    if (!keyword) {
      renderTable(allRows);
      return;
    }

    const filtered = allRows.filter(row =>
      headers.some(header =>
        String(row[header] || "").toLowerCase().includes(keyword)
      )
    );

    renderTable(filtered);
  });
});
</script>

