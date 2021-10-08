<template>
  <div
    class="block"
    :style="{
      height: Config.BLOCK_SIZE + Config.BLOCK_BORDER + 'px',
      width: Config.BLOCK_SIZE + Config.BLOCK_BORDER + 'px',
      borderWidth: Config.BLOCK_BORDER + 'px',
    }"
  >
    <div
      class="block-inside"
      :style="{
        height: Config.BLOCK_SIZE - Config.BLOCK_BORDER + 1 + 'px',
        width: Config.BLOCK_SIZE - Config.BLOCK_BORDER + 1 + 'px',
        color: light ? '#666666' : '#a0a0a0',
        backgroundColor: light ? colors[colorId][0] : colors[colorId][1],
        boxShadow: light
          ? 'inset 0 0 5px rgba(255, 255, 255, 0.8)'
          : 'inset 0 0 5px rgba(0, 0, 0, 0.4)',
      }"
    >
      {{ ox }}
    </div>
  </div>
</template>

<script>
import Config from "../const.js";

export default {
  name: "Block",
  props: {
    ox: String,
    light: Boolean,
    colorId: Number,
  },
  data: function () {
    return {
      colors: [
        ["#d9d9d9", "#757575"],
        ["#907cd9", "#4e4375"],
        ["#71bfd9", "#3d6775"],
        ["#d9808f", "#75454d"],
        ["#d9b882", "#756346"],
      ],
    };
  },
  computed: {
    Config() {
      return Config;
    },
  },
};
</script>

<style>
.block {
  position: absolute;
  border: solid 0px white;
  border-radius: 3px;
  background-color: rgba(0, 0, 0, 0.2);
  z-index: auto;
}

.block-inside {
  position: relative;
  border-radius: 1px;
  text-align: center;
  font-family: "Quicksand", sans-serif;
  font-size: 40px;
  z-index: -1;
}
</style>
