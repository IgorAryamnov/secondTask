<template>
  <button
    :disabled="disabled"
    :class="{
      button: true,
      button_disabled: disabled,
    }"
    :type="type"
  >
    {{ label }}
  </button>
</template>

<script>
export default {
  name: "StartButton",
  props: {
    type: {
      type: String,
      default: "submit",
    },
    label: {
      type: String,
    },
    disabled: Boolean,
  },
};
</script>

<style scoped>
.button {
  border: 2px solid rgb(66, 62, 190);
  background: rgb(55, 33, 222);
  display: inline-block;
  position: relative;
  min-width: 100px;
  text-transform: uppercase;
  padding: 10px 20px;
  text-align: center;
  border-radius: 22px;
  box-sizing: border-box;
  font-size: 12px;
  color: #fff;
}
.button:hover {
  cursor: pointer;
  background: rgb(69, 47, 239);
}
</style>
