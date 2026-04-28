---
title: Final Project Data Overview
permalink: /final-project/data-overview/
---

# Final Project Data Overview

This page lists the datasets used in the final project and provides searchable previews of the project data files.

The project uses four main datasets: the medicated feed mill license dataset, the refined Iowa CAFO dataset, the county-level ACS socioeconomic dataset, and the final joined county-level analysis table.

---

## 1. Medicated Feed Mill License Dataset

**File name:** `Medicated_Feed_Mill_Licenses_with_County.csv`  
**Purpose:** This dataset identifies approved medicated feed mill facilities in Iowa and assigns each facility to a county. It was used to calculate county-level medicated feed mill counts.

<input type="text" id="search-feedmill" placeholder="Search this table" style="width: 100%; max-width: 500px; padding: 10px; margin: 10px 0 20px 0; font-size: 16px;">

<div style="overflow-x: auto;">
<table id="table-feedmill" style="border-collapse: collapse; width: 100%; font-size: 14px;">
  <thead></thead>
  <tbody></tbody>
</table>
</div>

---

## 2. Refined Iowa CAFO Dataset

**File name:** `Refined Iowa CAFO Data 1.xlsx`  
**Purpose:** This dataset contains Iowa CAFO records. It was reviewed and filtered to identify beef-related CAFO facilities and summarize beef CAFO counts by county.

<input type="text" id="search-cafo" placeholder="Search this table" style="width: 100%; max-width: 500px; padding: 10px; margin: 10px 0 20px 0; font-size: 16px;">

<div style="overflow-x: auto;">
<table id="table-cafo" style="border-collapse: collapse; width: 100%; font-size: 14px;">
  <thead></thead>
  <tbody></tbody>
</table>
</div>

---

## 3. Iowa County ACS 2024 Dataset

**File name:** `iowa_county_acs5_2024.csv`  
**Purpose:** This dataset provides county-level socioeconomic indicators from the American Community Survey 5-year estimates. Variables include total population, median household income, civilian labor force, employed population, unemployed population, and unemployment rate.

<input type="text" id="search-acs" placeholder="Search this table" style="width: 100%; max-width: 500px; padding: 10px; margin: 10px 0 20px 0; font-size: 16px;">

<div style="overflow-x: auto;">
<table id="table-acs" style="border-collapse: collapse; width: 100%; font-size: 14px;">
  <thead></thead>
  <tbody></tbody>
</table>
</div>

---

## 4. Final County-Level Analysis Table

**File name:** `county_analysis_table.csv`  
**Purpose:** This is the final joined dataset used for Tableau dashboard development. It combines county-level medicated feed mill counts, beef CAFO counts, population, median household income, employment indicators, and unemployment rate.

<input type="text" id="search-analysis" placeholder="Search this table" style="width: 100%; max-width: 500px; padding: 10px; margin: 10px 0 20px 0; font-size: 16px;">

<div style="overflow-x: auto;">
<table id="table-analysis" style="border-collapse: collapse; width: 100%; font-size: 14px;">
  <thead></thead>
  <tbody></tbody>
</table>
</div>

---

<script src="https://cdn.jsdelivr.net/npm/papaparse@5.4.1/papaparse.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/xlsx@0.18.5/dist/xlsx.full.min.js"></script>

<script>
document.addEventListener("DOMContentLoaded", function () {

  function escapeHtml(value) {
    return String(value ?? "")
      .replace(/&/g, "&amp;")
      .replace(/</g, "&lt;")
      .replace(/>/g, "&gt;")
      .replace(/"/g, "&quot;")
      .replace(/'/g, "&#039;");
  }

  function renderTable(tableId, rows, headers) {
    const table = document.getElementById(tableId);
    const thead = table.querySelector("thead");
    const tbody = table.querySelector("tbody");

    thead.innerHTML = "";
    tbody.innerHTML = "";

    if (!headers || headers.length === 0) {
      tbody.innerHTML = "<tr><td>No data available.</td></tr>";
      return;
    }

    const headerRow = document.createElement("tr");

    headers.forEach(function (header) {
      const th = document.createElement("th");
      th.textContent = header;
      th.style.border = "1px solid #ccc";
      th.style.padding = "8px";
      th.style.backgroundColor = "#f2f2f2";
      th.style.textAlign = "left";
      th.style.position = "sticky";
      th.style.top = "0";
      headerRow.appendChild(th);
    });

    thead.appendChild(headerRow);

    rows.forEach(function (row) {
      const tr = document.createElement("tr");

      headers.forEach(function (header) {
        const td = document.createElement("td");
        td.innerHTML = escapeHtml(row[header] || "");
        td.style.border = "1px solid #ccc";
        td.style.padding = "8px";
        td.style.verticalAlign = "top";
        tr.appendChild(td);
      });

      tbody.appendChild(tr);
    });
  }

  function addSearch(searchId, tableId, allRows, headers) {
    const searchInput = document.getElementById(searchId);

    searchInput.addEventListener("input", function () {
      const keyword = this.value.trim().toLowerCase();

      if (!keyword) {
        renderTable(tableId, allRows, headers);
        return;
      }

      const filteredRows = allRows.filter(function (row) {
        return headers.some(function (header) {
          return String(row[header] || "").toLowerCase().includes(keyword);
        });
      });

      renderTable(tableId, filteredRows, headers);
    });
  }

  function loadCSV(csvUrl, tableId, searchId) {
    Papa.parse(csvUrl, {
      download: true,
      header: true,
      skipEmptyLines: true,
      complete: function (results) {
        const rows = results.data;
        const headers = results.meta.fields || [];

        renderTable(tableId, rows, headers);
        addSearch(searchId, tableId, rows, headers);
      },
      error: function (err) {
        const table = document.getElementById(tableId);
        table.querySelector("tbody").innerHTML =
          "<tr><td>Failed to load CSV: " + escapeHtml(err.message) + "</td></tr>";
      }
    });
  }

  function loadXLSX(xlsxUrl, tableId, searchId) {
    fetch(xlsxUrl)
      .then(function (response) {
        if (!response.ok) {
          throw new Error("Failed to load XLSX file.");
        }
        return response.arrayBuffer();
      })
      .then(function (data) {
        const workbook = XLSX.read(data, { type: "array" });
        const firstSheetName = workbook.SheetNames[0];
        const worksheet = workbook.Sheets[firstSheetName];

        const rows = XLSX.utils.sheet_to_json(worksheet, { defval: "" });
        const headers = rows.length > 0 ? Object.keys(rows[0]) : [];

        renderTable(tableId, rows, headers);
        addSearch(searchId, tableId, rows, headers);
      })
      .catch(function (err) {
        const table = document.getElementById(tableId);
        table.querySelector("tbody").innerHTML =
          "<tr><td>Failed to load XLSX: " + escapeHtml(err.message) + "</td></tr>";
      });
  }

  loadCSV("{{ '/final-project/Medicated_Feed_Mill_Licenses_with_County.csv' | relative_url }}",
          "table-feedmill",
          "search-feedmill");

  loadXLSX("{{ '/final-project/Refined%20Iowa%20CAFO%20Data%201.xlsx' | relative_url }}",
           "table-cafo",
           "search-cafo");

  loadCSV("{{ '/final-project/iowa_county_acs5_2024.csv' | relative_url }}",
          "table-acs",
          "search-acs");

  loadCSV("{{ '/final-project/county_analysis_table.csv' | relative_url }}",
          "table-analysis",
          "search-analysis");

});
</script>
