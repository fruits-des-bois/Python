<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="utf-8">
<title>Observations météo</title>

<style>
  body{
    font-family:system-ui,-apple-system,Segoe UI,Roboto,Arial;
    margin:20px;
  }

  table{
    border-collapse:collapse;
    width:100%;
    max-width:500px;
  }

  th,td{
    border:1px solid #ddd;
    padding:8px;
  }

  th{
    background:#f3f3f3;
  }

  td.num{
    text-align:right;
  }

  td.txt{
    text-align:left;
  }
</style>
</head>

<body>

<table id="tbl">
  <thead>
    <tr>
      <th>Date</th>
      <th>Heure</th>
      <th>Précipitations (mm)</th>
    </tr>
  </thead>
  <tbody></tbody>
</table>

<script>

const url =
  "https://api.open-meteo.com/v1/forecast?latitude=49.4333&longitude=2.0833&hourly=precipitation&timezone=Europe/Paris";

fetch(url)
  .then(r => r.json())
  .then(j => {

    const tbody = document.querySelector("#tbl tbody");

    const times = j.hourly.time;
    const precipitation = j.hourly.precipitation;

    for(let i = 0; i < times.length; i++) {

      const tr = document.createElement("tr");

      const dt = new Date(times[i]);

      const datePart = dt.toLocaleDateString("fr-FR");
      const timePart = dt.toLocaleTimeString("fr-FR", {
        hour: "2-digit",
        minute: "2-digit"
      });

      tr.innerHTML = `
        <td class="txt">${datePart}</td>
        <td class="txt">${timePart}</td>
        <td class="num">${precipitation[i] ?? "—"}</td>
      `;

      tbody.appendChild(tr);
    }
  })
  .catch(e => {
    document.querySelector("#tbl tbody").innerHTML =
      `<tr><td colspan="4">Erreur : ${e.message}</td></tr>`;
  });

</script>

</body>
</html>
