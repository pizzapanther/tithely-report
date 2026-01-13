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
  <ReportSummary :stats="stats"/>
  <ReportTransactions :transactions="rows"/>
</template>
<script setup>
import {ref} from 'vue';
import * as Papa from 'papaparse';

import ReportSummary from './Summary.vue';
import ReportTransactions from './Transactions.vue';

const REQUIRED_FIELDS = [
  'Currency Code', 'First Name', 'Last Name', 'Net Amount',
  'Fees', 'Status', 'Covered Fees', 'Fund Name', 'Payment Type', 'Check #'
];

const rows = ref(null);
const stats = ref(null);

function error (e) {
  alert('Error parsing CSV');
  console.error(e);
}

function map_rows(rows) {
  const headers = rows[0];
  let data = rows.slice(1);
  const mapped = {};
  const stats = {
    cash: 0,
    check: 0,
    online: 0,
    fees: 0,
    covered: 0,
    total: 0,
    funds: {},
  };

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

      stats.fees +=  parseFloat(row['Fees']);
      if (row['Covered Fees']) {
        stats.covered +=  parseFloat(row['Fees']);
      }

      if (row['Payment Type'] === 'cash') {
        stats.cash += row['Net Amount'];
      } else if (row['Payment Type'] === 'check') {
        stats.check += row['Net Amount'];
      } else {
        stats.online += row['Net Amount'];
      }

      stats.total += row['Net Amount'];

      if (stats.funds?.[row['Fund Name']]) {
        stats.funds[row['Fund Name']] += row['Net Amount'];
      } else {
        stats.funds[row['Fund Name']] = row['Net Amount'];
      }

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

  const [rlist, rstats] = map_rows(results.data);
  rows.value = rlist;
  stats.value = rstats;
}

function process() {
  const csv_file = document.getElementById("csv_file");
  Papa.parse(csv_file.files[0], {complete, error});
}

</script>
