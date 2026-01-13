<template>
  <div v-if="props.transactions">
    <table>
      <thead>
        <tr>
          <th>Name/Funds</th>
          <th>Type/Check #</th>
          <th>$ Total</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="row in transactions">
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
</template>
<script setup>
  const props = defineProps(['transactions']);
</script>
<style scoped>
  th {
    font-weight: bold;
  }

  td {
    vertical-align: top;
    font-size: 14px;
    padding: 5px 10px !important;
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
