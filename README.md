<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="utf-8">
<title>Prévisions météo</title>

<style>
  body{
    font-family:system-ui,Arial,sans-serif;
    margin:20px;
  }

  table{
    border-collapse:collapse;
    width:100%;
    max-width:700px;
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

    // Maintenant
    const now = new Date();

    // Maintenant + 1 heure
    const startDate = new Date(now.getTime() + 60 * 60 * 1000);

    // Fin = J+4
    const endDate = new Date(now);
    endDate.setDate(endDate.getDate() + 4);

    for(let i = 0; i < times.length; i++) {

      const dt = new Date(times[i]);

      // Filtre entre +1h et J+4
      if(dt >= startDate && dt <= endDate) {

        const tr = document.createElement("tr");

        tr.innerHTML = `
          <td>${dt.toLocaleDateString("fr-FR")}</td>
          <td>${dt.toLocaleTimeString("fr-FR", {
            hour: "2-digit",
            minute: "2-digit"
          })}</td>
          <td class="num">${precipitation[i] ?? "—"}</td>
        `;

        tbody.appendChild(tr);
      }
    }
  })
  .catch(e => {

    document.querySelector("#tbl tbody").innerHTML =
      `<tr><td colspan="3">Erreur : ${e.message}</td></tr>`;

  });

</script>

</body>
</html>
