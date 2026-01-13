<template>
  <h1>Tithe.ly Better Reporting</h1>
  <form>
    <fieldset>
      <label>
        <span>CSV File</span>
        <br>
        <span class="help">
          <strong>Required Fields:</strong> Net Amount, Name, Fees, Status,
          Covered Fees, Fund Name, Payment Type, Check #
        </span>
        <input id="csv_file" type="file" name="csv" placeholder="CSV" accept=".csv" v-on:change="process()"/>
      </label>
    </fieldset>
  </form>
  <table>
    <thead>
      <tr>
        <th>Name/Funds</th>
        <th>Type/Check #</th>
        <th>$ Total</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="row in rows">
        <td>
          <strong>{{row['fullname']}}</strong>
          <br>
          <div class="funds">
            <div v-for="(value, cat) in row['cats']">
              {{cat}}: ${{value.toFixed(2)}}
            </div>
          </div>
        </td>
        <td>
          {{row['Payment Type']}}
          <div v-if="row['Payment Type'] === 'check'">
            # {{row['Check #']}}
          </div>
        </td>
        <td>
          {{row['total'].toFixed(2)}}
        </td>
      </tr>
    </tbody>
  </table>
</template>
<script setup>
import {ref} from 'vue';
import * as Papa from 'papaparse';

const REQUIRED_FIELDS = [
  'Currency Code', 'First Name', 'Last Name', 'Net Amount',
  'Fees', 'Status', 'Covered Fees', 'Fund Name', 'Payment Type', 'Check #'
];

const rows = ref([]);

function error (e) {
  alert('Error parsing CSV');
  console.error(e);
}

function map_rows(rows) {
  const headers = rows[0];
  let data = rows.slice(1);
  const mapped = {};
  const stats = {};

  for (let i=0; i < data.length; i++) {
    const d = data[i];
    const row = {};

    for (let j=0; j < headers.length; j++) {
      const key = headers[j];
      row[key] = d[j];
    }

    if (row['Net Amount']) {
      row['Net Amount'] = parseFloat(row['Net Amount']);
      row['fullname'] = `${row['Last Name']}, ${row['First Name']}`;
      const key = `${row['Last Name']}, ${row['First Name']} ${row['Payment Type']} ${row['Check #']}`;

      if (mapped?.[key]) {
        if (mapped[key]['cats']?.[row['Fund Name']]) {
          mapped[key]['cats'][row['Fund Name']] += row['Net Amount'];
        } else {
          mapped[key]['cats'][row['Fund Name']] = row['Net Amount'];
        }

        mapped[key]['total'] += row['Net Amount'];
      } else {
        row['cats'] = {}
        row['cats'][row['Fund Name']] = row['Net Amount'];
        row['total'] = row['Net Amount'];
        mapped[key] = row;
      }
    }
  }

  const keys = Object.keys(mapped);
  keys.sort();

  const ret = [];
  keys.forEach((k) => {
    ret.push(mapped[k]);
  });

  return [ret, stats];
}

function complete(results) {
  for (let i=0; i < REQUIRED_FIELDS.length; i++) {
    const field = REQUIRED_FIELDS[i];
    if (!results.data[0].includes(field)) {
      alert(`Missing Field: ${field}`);
      return;
    }
  }

  const [rlist, stats] = map_rows(results.data);
  rows.value = rlist;
}

function process() {
  const csv_file = document.getElementById("csv_file");
  Papa.parse(csv_file.files[0], {complete, error});
}

</script>
