<template>
  <div
    id="hint"
    :style="{
      height: Config.HINT_HEIGHT + 'px',
      width: Config.HINT_WIDTH + 'px',
    }"
  >
    <transition name="fade" mode="out-in">
      <div id="phase1" v-if="phase1" key="phase1">
        <div class="message">光のあるべき<br />場所を選べ</div>
        <div
          id="block-container"
          :style="{
            height:
              3 * Config.BLOCK_SIZE +
              Config.GRID_BORDER * 2 -
              Config.BLOCK_BORDER +
              'px',
            width:
              3 * Config.BLOCK_SIZE +
              Config.GRID_BORDER * 2 -
              Config.BLOCK_BORDER +
              'px',
            borderWidth: Config.GRID_BORDER + 'px',
          }"
        >
          <transition name="fade">
            <div
              id="result"
              :style="{
                height: 3 * Config.BLOCK_SIZE - Config.BLOCK_BORDER + 'px',
                width: 3 * Config.BLOCK_SIZE - Config.BLOCK_BORDER + 'px',
              }"
              v-if="isright"
            >
              正解
            </div>
            <div
              id="result"
              :style="{
                height: 3 * Config.BLOCK_SIZE - Config.BLOCK_BORDER + 'px',
                width: 3 * Config.BLOCK_SIZE - Config.BLOCK_BORDER + 'px',
              }"
              v-if="iswrong"
            >
              不正解
            </div>
          </transition>
          <Block
            class="clickable"
            v-for="bl in blocks"
            :id="'block-' + bl.id"
            :key="bl.id"
            :style="{
              top: bl.x * Config.BLOCK_SIZE - Config.BLOCK_BORDER + 'px',
              left: bl.y * Config.BLOCK_SIZE - Config.BLOCK_BORDER + 'px',
            }"
            :light="bl.light"
            :colorId="0"
            :ox="bl.ox"
            @click.native="toggle"
          />
        </div>
        <div class="button" @click="check">判定</div>
      </div>
      <div id="phase2" v-else-if="!clear" key="phase2">
        <div class="message">すべて配置し<br />光で満たせ</div>
        <img class="image" src="../assets/paper.png" />
      </div>
      <div id="clear" v-else key="phaseclear">
        <div class="message">
          <span
            style="
              font-family: 'Quicksand', sans-serif;
              letter-spacing: 2px;
              font-size: 40px;
            "
            >OX25</span
          >
          の謎を<br />解き明かした！
        </div>
        <div class="tweet">
          <a
            href="https://twitter.com/intent/tweet?text=OX25 の謎を解き明かした！&hashtags=OX25謎&url=https://yamunaku.github.io/ox25/"
            class="twitter-share-button"
            >twitter でシェア</a
          >
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import Config from "../const.js";
import Block from "./Block.vue";

export default {
  name: "Hint",
  components: {
    Block,
  },
  props: {
    clear: Boolean,
  },
  data: function () {
    return {
      iswrong: false,
      isright: false,
      phase1: true,
      blocks: [
        { id: 0, x: 0, y: 0, light: false, ox: "O" },
        { id: 1, x: 0, y: 1, light: false, ox: "X" },
        { id: 2, x: 0, y: 2, light: false, ox: "O" },
        { id: 3, x: 1, y: 0, light: false, ox: "O" },
        { id: 4, x: 1, y: 2, light: false, ox: "X" },
        { id: 5, x: 2, y: 0, light: false, ox: "X" },
        { id: 6, x: 2, y: 1, light: false, ox: "X" },
        { id: 7, x: 2, y: 2, light: false, ox: "O" },
      ],
      ans: [true, false, true, false, false, false, true, true],
    };
  },
  methods: {
    toggle: function (e) {
      var id = Number(e.target.id[6]);
      this.blocks[id].light = !this.blocks[id].light;
    },
    deleteWrong: function () {
      this.iswrong = false;
    },
    deleteRight: function () {
      this.isright = false;
      this.phase1 = false;
      this.$emit("nextPhase");
    },
    check: function () {
      var ok = true;
      for (var i = 0; i < 8; i++) {
        if (this.blocks[i].light != this.ans[i]) {
          ok = false;
        }
      }
      if (ok) {
        this.isright = true;
        setTimeout(this.deleteRight, 500);
      } else {
        this.iswrong = true;
        setTimeout(this.deleteWrong, 500);
      }
    },
  },
  computed: {
    Config() {
      return Config;
    },
  },
};
</script>

<style>
#hint {
  position: relative;
  flex: none;
  border: solid 1px #c2c2c2;
  border-radius: 3px;
  display: flex;
  flex-direction: column;
  flex-wrap: nowrap;
  justify-content: center;
  align-items: center;
}

.clickable {
  cursor: pointer;
}

.message {
  font-size: 30px;
  text-align: center;
}

.button {
  width: 100px;
  height: 100px;
  font-size: 40px;
  border-radius: 3px;
  text-align: center;
  line-height: 90px;
  margin: auto;
  position: relative;
  cursor: pointer;
  background-color: gray;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.8);
  transition: box-shadow 0.3s, color 0.3s, background-color 0.3s;
}

.button:hover {
  color: #666666;
  background-color: white;
  box-shadow: 0 0 2px rgba(0, 0, 0, 0.8);
}

.button:active {
  color: #666666;
  background-color: white;
  box-shadow: 0 0 2px rgba(0, 0, 0, 0.8);
}

.image {
  display: block;
  width: 240px;
  height: 150px;
  margin: 20px auto;
}

#block-container {
  position: relative;
  margin: 20px auto;
  border: solid 0px white;
  border-radius: 3px;
  background-color: white;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.8);
  z-index: 0;
}

#result {
  position: relative;
  color: #666666;
  background-color: white;
  font-size: 40px;
  text-align: center;
  line-height: 138px;
  z-index: 100;
}

.tweet {
  margin: 20px auto;
}

.twitter-share-button {
  display: inline-block;
  width: 200px;
  height: 40px;
  font-size: 20px;
  border-radius: 3px;
  text-align: center;
  line-height: 40px;
  margin: auto;
  position: relative;
  cursor: pointer;
  background-color: gray;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.8);
  transition: box-shadow 0.3s, color 0.3s, background-color 0.3s;
}

.twitter-share-button:hover {
  color: #666666;
  background-color: white;
  box-shadow: 0 0 2px rgba(0, 0, 0, 0.8);
}

.twitter-share-button:active {
  color: #666666;
  background-color: white;
  box-shadow: 0 0 2px rgba(0, 0, 0, 0.8);
}
</style>
