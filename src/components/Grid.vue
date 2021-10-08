<template>
  <div
    id="grid"
    :style="{
      height:
        Config.BLOCK_SIZE * 5 +
        Config.GRID_BORDER * 2 -
        Config.BLOCK_BORDER +
        'px',
      width:
        Config.BLOCK_SIZE * 5 +
        Config.GRID_BORDER * 2 -
        Config.BLOCK_BORDER +
        'px',
      borderWidth: Config.GRID_BORDER + 'px',
    }"
  >
    <Block
      v-for="bl in blocks"
      :key="bl.id"
      :style="{
        top: bl.x * Config.BLOCK_SIZE - Config.BLOCK_BORDER + 'px',
        left: bl.y * Config.BLOCK_SIZE - Config.BLOCK_BORDER + 'px',
      }"
      :light="light[bl.x][bl.y]"
      :colorId="0"
      ox="X"
    />
  </div>
</template>

<script>
import Config from "../const.js";
import Block from "./Block.vue";

export default {
  name: "Grid",
  props: { light: Array },
  components: {
    Block,
  },
  data: function () {
    var bl_array = [];
    for (var i = 0; i < 5; i++) {
      for (var j = 0; j < 5; j++) {
        bl_array.push({
          id: i * 5 + j,
          x: i,
          y: j,
        });
      }
    }
    return { blocks: bl_array };
  },
  computed: {
    Config() {
      return Config;
    },
  },
};
</script>

<style>
#grid {
  position: absolute;
  border: solid 0px white;
  border-radius: 3px;
  background-color: #3a3a3a;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.8);
}
</style>
