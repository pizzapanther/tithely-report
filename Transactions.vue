<template>
  <div v-if="props.col1" class="flex">
    <div v-for="col in [col1, col2]">
      <table>
        <thead>
          <tr>
            <th>Name/Funds</th>
            <th>Type/Check #</th>
            <th>$ Total</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="row in col">
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
              <div class="cap">{{row['Payment Type']}}</div>
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
    </div>
  </div>
</template>
<script setup>
  const props = defineProps(['col1', 'col2']);
</script>
<style scoped>
  .flex > div {
    width: calc(50% - 20px);
    margin: 0 10px;
  }

  table {
    width: 100%;
  }

  th {
    font-weight: bold;
    padding: 3px 10px !important;
    font-size: 12px;
  }

  td {
    vertical-align: top;
    font-size: 12px;
    padding: 3px 10px !important;
  }

  tbody tr:nth-child(odd) td {
    background-color: var(--pico-table-border-color);
  }

  .funds {
    font-size: 80%;
  }

  .cap {
    text-transform: capitalize;
  }
</style>
