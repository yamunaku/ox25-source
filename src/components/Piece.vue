<template>
  <div class="piece">
    <Block
      v-for="sh in shape"
      :key="sh.id"
      :style="{
        top: sh.x * Config.BLOCK_SIZE + 'px',
        left: sh.y * Config.BLOCK_SIZE + 'px',
      }"
      :light="sh.light"
      :colorId="colorId"
      ox="O"
    />
  </div>
</template>

<script>
import Config from "../const.js";
import Block from "./Block.vue";

export default {
  name: "Piece",
  props: {
    shape: Array,
    colorId: Number,
  },
  components: {
    Block,
  },
  data: function () {
    return {};
  },
  computed: {
    Config() {
      return Config;
    },
  },
};
</script>

<style>
.piece {
  position: absolute;
  cursor: pointer;
}
</style>
