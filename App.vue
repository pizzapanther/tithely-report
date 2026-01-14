<template>
  <div class="header flex">
    <h1>Service Report</h1>
    <div v-if="rdate">{{rdate}}</div>
  </div>
  <form>
    <fieldset>
      <div class="grid">
        <div>
          <label>
            Report Date
            <input id="rdate" type="date" name="rdate" placeholder="Report Date" v-on:change="update()"/>
          </label>
        </div>
        <div>
          Counters (one per line)
          <textarea name="counters" id="counters" v-on:change="update_counters()"></textarea>
        </div>
      </div>
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
  <ReportSummary :stats="stats" :counters="counters"/>
  <hr>
  <ReportTransactions :col1="rows1" :col2="rows2"/>
  <footer>
    www.giving.report contribute at:
    <a href="https://github.com/pizzapanther/tithely-report" target="_blank">
      github.com/pizzapanther/tithely-report
    </a>
    <br><br>
    Everything is generated in the browser so none of you information is stored.
  </footer>
</template>
<script setup>
import {ref, onMounted} from 'vue';
import * as Papa from 'papaparse';
import { DateTime } from 'luxon';

import ReportSummary from './Summary.vue';
import ReportTransactions from './Transactions.vue';

const REQUIRED_FIELDS = [
  'Currency Code', 'First Name', 'Last Name', 'Net Amount',
  'Fees', 'Status', 'Covered Fees', 'Fund Name', 'Payment Type', 'Check #'
];

const rows1 = ref(null);
const rows2 = ref(null);
const stats = ref(null);
const rdate = ref(null);
const counters = ref([]);


onMounted(() => {
  const now = DateTime.now();
  const d = document.getElementById("rdate");
  d.value = now.toISODate();
  rdate.value = now.toLocaleString(DateTime.DATE_MED_WITH_WEEKDAY);
});

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

  const ret1 = [];
  const ret2 = [];

  const halfi = Math.ceil(keys.length / 2);
  const keys1 = keys.slice(0, halfi);
  const keys2 = keys.slice(halfi);

  keys1.forEach((k) => {
    ret1.push(mapped[k]);
  });

  keys2.forEach((k) => {
    ret2.push(mapped[k]);
  });

  stats.notcovered = stats.fees - stats.covered;

  return [ret1, ret2, stats];
}

function complete(results) {
  for (let i=0; i < REQUIRED_FIELDS.length; i++) {
    const field = REQUIRED_FIELDS[i];
    if (!results.data[0].includes(field)) {
      alert(`Missing Field: ${field}`);
      return;
    }
  }

  const [rlist1, rlist2, rstats] = map_rows(results.data);
  rows1.value = rlist1;
  rows2.value = rlist2;
  stats.value = rstats;
}

function process() {
  const csv_file = document.getElementById("csv_file");
  Papa.parse(csv_file.files[0], {complete, error});
}

function update() {
  const d = document.getElementById("rdate");
  if (d.value) {
    const dt = DateTime.fromISO(d.value);
    rdate.value = dt.toLocaleString(DateTime.DATE_MED_WITH_WEEKDAY);
  }
}

function update_counters() {
  const c = document.getElementById("counters");
  if (c.value) {
    counters.value = c.value.split("\n");
  } else {
    counters.value = [];
  }
}

</script>
<style scoped>
  h1 {
    font-size: 24px;
    flex: 1;
    padding: 0 !important;
    margin: 0 !important;
  }

  .header {
    border-bottom: 2px var(--pico-table-border-color) solid;
    margin-bottom: 15px;
  }

  footer {
    margin-top: 40px;
    text-align: center;
    font-size: 12px;
  }
</style>
