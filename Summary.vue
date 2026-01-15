<template>
  <div v-if="props.stats" class="flex">
    <div>
      <table>
        <thead>
          <tr>
            <th colspan="2">Summary</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>Cash</td>
            <td>${{props.stats.cash.toFixed(2)}}</td>
          </tr>
          <tr>
            <td>Check</td>
            <td>${{props.stats.check.toFixed(2)}}</td>
          </tr>
          <tr>
            <td>Online</td>
            <td>${{props.stats.online.toFixed(2)}}</td>
          </tr>
        </tbody>
        <tfoot>
          <tr>
            <th>Total</th>
            <th>${{props.stats.total.toFixed(2)}}</th>
          </tr>
        </tfoot>
      </table>
      <table>
        <thead>
          <tr>
            <th colspan="2">Fees</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>Fees Covered</td>
            <td>${{props.stats.covered.toFixed(2)}}</td>
          </tr>
          <tr>
            <td>Not Covered</td>
            <td>${{props.stats.notcovered.toFixed(2)}}</td>
          </tr>
        </tbody>
        <tfoot>
          <tr>
            <th>Total</th>
            <th>${{props.stats.fees.toFixed(2)}}</th>
          </tr>
        </tfoot>
      </table>
    </div>
    <div>
      <table>
        <thead>
          <tr>
            <th colspan="2">Funds Summary</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(value, fund) in stats.funds">
            <td class="fund">{{fund}}</td>
            <td>${{value.toFixed(2)}}</td>
          </tr>
        </tbody>
        <tfoot>
          <tr>
            <th>Total</th>
            <th>${{props.stats.total.toFixed(2)}}</th>
          </tr>
        </tfoot>
      </table>
    </div>
    <div>
      <table>
        <thead>
          <tr>
            <th>Counters</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="c in props.counters">
            <td>
              <hr>
              {{c}}
              <br><br>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>
<script setup>
  const props = defineProps(['stats', 'counters']);
</script>
<style scoped>
  .flex {
    font-size: 16px;
    justify-content: space-between;

    & > div {
      margin: 0;
    }
  }

  table {
    margin-bottom: 10px;
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

  hr {
    margin: 50px 0 5px 0;
    padding: 0;
    width: 180px;
    border-top-width: 2px;
    border-top-style: dashed;
  }

  .fund {
    width: 160px;
  }
</style>
