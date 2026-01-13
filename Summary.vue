<template>
  <div v-if="props.stats">
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
</template>
<script setup>
  const props = defineProps(['stats']);
</script>
