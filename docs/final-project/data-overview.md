---
title: "Final Project Data Overview"
permalink: /final-project/data-overview/
---

Final Project Data Overview - here you will list the data you use and the location or source of that data.

<script src="https://code.jquery.com/jquery-3.7.1.min.js"></script>
<link rel="stylesheet"
href="https://cdn.datatables.net/1.13.6/css/jquery.dataTables.min.css"/>
<script src="https://cdn.datatables.net/1.13.6/js/jquery.dataTables.min.js"></script>

<script src="https://cdn.jsdelivr.net/npm/papaparse@5.4.1/papaparse.min.js"></script>

<h2>Medicated Feed Mill License Dataset</h2>

<table id="csvTable" class="display" style="width:100%"></table>

<script>
Papa.parse("/CRP558/docs/final-project/Medicated_Feed_Mill_Licenses_with_County.csv", {
download: true,
header: true,
complete: function(results) {

$('#csvTable').DataTable({
data: results.data,
pageLength: 15,
scrollX: true,
columns: Object.keys(results.data[0]).map(function(col){
return { title: col, data: col };
})

});

}
});
</script>
