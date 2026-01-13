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
            <td>{{fund}}</td>
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
          <tr>
            <td>
              <ul>
                <li v-for="c in props.counters">{{c}}</li>
              </ul>
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
    justify-content: center;

    & > div {
      margin: 0 40px;
    }
  }
</style>
