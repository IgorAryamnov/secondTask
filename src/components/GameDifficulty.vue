<template>
  <div>
    <div class="selectWrap">
      <label
        v-for="button in buttons"
        :disabled="disabled"
        tabindex="0"
        @keyup.enter="updateInputData(button.name)"
        :key="button.name"
        :for="button.name"
        :class="{
          label: true,
          label_disabled: disabled,
        }"
      >
        {{ button.nameLocale }}
        <input
          class="levelInput"
          type="radio"
          :disabled="disabled"
          :name="name"
          :value="button.name"
          :id="button.name"
          :checked="button.name === value"
          @change="updateInputData($event.target.value)"
        />
      </label>
    </div>
  </div>
</template>

<script>
export default {
  name: "GameDifficulty",
  props: {
    disabled: Boolean,
    name: {
      type: String,
    },
    defaultValue: {
      type: String,
      default: "",
    },
    buttons: {
      type: Array,
    },
  },
  data() {
    return {
      value: this.defaultValue,
    };
  },
  methods: {
    updateInputData(value) {
      this.value = value;
      this.$emit("update-input-data", value);
    },
  },
};
</script>

<style scoped>
.selectWrap {
  display: flex;
  flex-direction: column;
  gap: 10px;
}
.label {
  cursor: pointer;
  display: flex;
  align-items: center;
  font-size: 15px;
}
.label_disabled {
  opacity: 0.3;
  cursor: default;
}
.levelInput {
  cursor: pointer;
  order: -1;
}
</style>
