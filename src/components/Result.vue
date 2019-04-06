<template>
  <div v-if="features" id="feature-description">
    <span>結果:</span>
    <ul v-for="(value, key) in features" :key="key">
      <li>
        <span>{{ key }}:</span>
        <div>{{ prettyPrint(value) }}</div>
      </li>
    </ul>
  </div>
</template>
<script>
export default {
  props: {
    features: Object
  },
  methods: {
    prettyPrint(value) {
      if (typeof value === "object") {
        const pretty = JSON.stringify(value, null, 8);
        return pretty;
      }
      return value;
    }
  }
};
</script>

<style>
ul {
  list-style: none;
}
li {
  display: flex;
}
li * {
  width: 50%;
  white-space: pre;
  text-align: left;
}
</style>
