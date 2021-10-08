<template>
  <div id="game">
    <div
      id="puzzle"
      :style="{
        height: Config.PUZZLE_HEIGHT + 'px',
        width: Config.PUZZLE_WIDTH + 'px',
      }"
      @pointerup="pieceMouseUp"
      @pointermove="pieceMouseMove"
      @pointerleave="pieceMouseUp"
    >
      <Piece
        v-for="pc in pieces"
        :id="'piece-' + pc.id"
        :key="pc.id"
        :shape="pc.shape"
        :style="{
          top: pc.pos.x + 'px',
          left: pc.pos.y + 'px',
          zIndex: pc.id == grabbing ? 200 : pc.id * 10 + 100,
        }"
        :colorId="pc.id + 1"
        @pointerdown.native="pieceMouseDown"
      />
      <Grid
        :style="{
          top: grid_pos.x + 'px',
          left: grid_pos.y + 'px',
          zIndex: 10,
        }"
        :light="light_grid"
      />
      <transition name="fade" mode="out-in">
        <div
          class="button"
          :style="{
            top: grid_pos.x + 150 + 'px',
            left: grid_pos.y + 10 + 'px',
            zIndex: 20,
          }"
          v-if="phase1"
          key="phase1"
          @click="lightOn"
        >
          確認
        </div>
        <div
          class="button"
          :style="{
            top: grid_pos.x + 150 + 'px',
            left: grid_pos.y + 10 + 'px',
            zIndex: 20,
          }"
          v-else
          key="phase2"
          @click="lightOn"
        >
          判定
        </div>
      </transition>
    </div>
    <Hint @nextPhase="phase1 = false" :clear="clear" />
  </div>
</template>

<script>
import Config from "../const.js";
import Piece from "./Piece.vue";
import Grid from "./Grid.vue";
import Hint from "./Hint.vue";

export default {
  name: "Game",
  components: {
    Piece,
    Grid,
    Hint,
  },
  data: function () {
    var init_pos = [
      { x: 240, y: 70 },
      { x: 330, y: 170 },
      { x: 70, y: 70 },
      { x: 330, y: 340 },
    ];
    var place = [];
    for (var i = 0; i < 5; i++) {
      var v = [];
      for (var j = 0; j < 5; j++) v.push(-1);
      place.push(v);
    }
    var light_grid = [];
    for (i = 0; i < 5; i++) {
      v = [];
      for (j = 0; j < 5; j++) v.push(false);
      light_grid.push(v);
    }
    return {
      phase1: true,
      clear: false,
      grid_pos: {
        x: 50,
        y: (Config.PUZZLE_WIDTH - Config.BLOCK_SIZE * 5) / 2,
      },
      place: place,
      light_grid: light_grid,
      pieces: [
        {
          id: 0,
          init_pos: init_pos[0],
          pos: Object.assign({}, init_pos[0]),
          shape: [
            { id: 0, x: 0, y: 0, light: false },
            { id: 1, x: 1, y: 0, light: false },
            { id: 2, x: 1, y: 1, light: false },
            { id: 3, x: 2, y: 0, light: false },
          ],
          max_shape: { x: 2, y: 1 },
        },
        {
          id: 1,
          init_pos: init_pos[1],
          pos: Object.assign({}, init_pos[1]),
          shape: [
            { id: 0, x: 0, y: 2, light: false },
            { id: 1, x: 1, y: 0, light: false },
            { id: 2, x: 1, y: 1, light: false },
            { id: 3, x: 1, y: 2, light: false },
          ],
          max_shape: { x: 1, y: 2 },
        },
        {
          id: 2,
          init_pos: init_pos[2],
          pos: Object.assign({}, init_pos[2]),
          shape: [
            { id: 0, x: 0, y: 1, light: false },
            { id: 1, x: 1, y: 0, light: false },
            { id: 2, x: 1, y: 1, light: false },
            { id: 3, x: 2, y: 0, light: false },
          ],
          max_shape: { x: 2, y: 1 },
        },
        {
          id: 3,
          init_pos: init_pos[3],
          pos: Object.assign({}, init_pos[3]),
          shape: [
            { id: 0, x: 0, y: 1, light: false },
            { id: 1, x: 0, y: 2, light: false },
            { id: 2, x: 1, y: 0, light: false },
            { id: 3, x: 1, y: 1, light: false },
          ],
          max_shape: { x: 1, y: 2 },
        },
      ],
      lighting: false,
      grabbing: -1,
      from_mouse: { x: 0, y: 0 },
      from_piece: { x: 0, y: 0 },
    };
  },
  methods: {
    lightOn: function () {
      if (this.clear) return;
      this.lighting = true;
      var light = [];
      var on_count = 0;
      for (var i = 0; i < 5; i++) {
        var light_row = [];
        for (var j = 0; j < 5; j++) {
          var ok = false;
          for (var k = 0; k < 8; k++) {
            var nx = i + Config.VX8[k],
              ny = j + Config.VY8[k];
            if (nx < 0 || 5 <= nx || ny < 0 || 5 <= ny) continue;
            if (this.place[nx][ny] == -1) ok = true;
          }
          if ((this.place[i][j] == -1) ^ ok) {
            light_row.push(true);
            on_count++;
          } else {
            light_row.push(false);
          }
        }
        light.push(light_row);
      }
      var mix = [5, 5, 5, 5],
        miy = [5, 5, 5, 5];
      for (i = 0; i < 5; i++) {
        for (j = 0; j < 5; j++) {
          if (this.place[i][j] != -1) {
            mix[this.place[i][j]] = Math.min(mix[this.place[i][j]], i);
            miy[this.place[i][j]] = Math.min(miy[this.place[i][j]], j);
          }
        }
      }
      for (i = 0; i < 4; i++) {
        var piece = this.pieces[i];
        if (mix[i] == 5) {
          for (j = 0; j < 4; j++) {
            piece.shape[j].light = false;
          }
          continue;
        }
        for (j = 0; j < 4; j++) {
          piece.shape[j].light =
            light[mix[i] + piece.shape[j].x][miy[i] + piece.shape[j].y];
          light[mix[i] + piece.shape[j].x][miy[i] + piece.shape[j].y] = false;
        }
      }
      this.light_grid = light;
      if (!this.phase1 && on_count == 25) {
        this.clear = true;
      }
    },
    lightOff: function () {
      if (this.clear) return;
      this.lighting = false;
      var off_grid = [];
      for (var i = 0; i < 5; i++) {
        var off_row = [];
        for (var j = 0; j < 5; j++) off_row.push(false);
        off_grid.push(off_row);
      }
      this.light_grid = off_grid;
      for (i = 0; i < 4; i++) {
        for (j = 0; j < 4; j++) {
          this.pieces[i].shape[j].light = false;
        }
      }
    },
    pieceMouseDown: function (e) {
      if (this.clear) return;
      this.grabbing = Number(e.target.parentNode.id[6]);
      this.from_mouse.x = e.y;
      this.from_mouse.y = e.x;
      this.from_piece.x = this.pieces[this.grabbing].pos.x;
      this.from_piece.y = this.pieces[this.grabbing].pos.y;
      for (var i = 0; i < 5; i++) {
        for (var j = 0; j < 5; j++) {
          if (this.place[i][j] == this.grabbing) this.place[i][j] = -1;
        }
      }
      if (this.lighting) this.lightOff();
    },
    pieceMouseUp: function (e) {
      if (this.clear) return;
      if (this.grabbing == -1) return;
      var id = this.grabbing;
      this.grabbing = -1;
      var piece = this.pieces[id];
      var px = Math.max(
        0,
        Math.min(
          Config.PUZZLE_HEIGHT - (piece.max_shape.x + 1) * Config.BLOCK_SIZE,
          e.y - this.from_mouse.x + this.from_piece.x
        )
      );
      var py = Math.max(
        0,
        Math.min(
          Config.PUZZLE_WIDTH - (piece.max_shape.y + 1) * Config.BLOCK_SIZE,
          e.x - this.from_mouse.y + this.from_piece.y
        )
      );
      var dx = Math.round(
        (px - (this.grid_pos.x + Config.GRID_BORDER - Config.BLOCK_BORDER)) /
          Config.BLOCK_SIZE
      );
      var dy = Math.round(
        (py - (this.grid_pos.y + Config.GRID_BORDER - Config.BLOCK_BORDER)) /
          Config.BLOCK_SIZE
      );
      if (
        dx < 0 ||
        5 - piece.max_shape.x <= dx ||
        dy < 0 ||
        5 - piece.max_shape.y <= dy
      ) {
        piece.pos.x = piece.init_pos.x;
        piece.pos.y = piece.init_pos.y;
        return;
      }
      var canput = true;
      for (var i = 0; i < 4; i++) {
        if (this.place[dx + piece.shape[i].x][dy + piece.shape[i].y] != -1)
          canput = false;
      }
      if (!canput) {
        piece.pos.x = piece.init_pos.x;
        piece.pos.y = piece.init_pos.y;
        return;
      }
      for (i = 0; i < 4; i++) {
        this.place[dx + piece.shape[i].x][dy + piece.shape[i].y] = id;
      }
      piece.pos.x =
        dx * Config.BLOCK_SIZE +
        (this.grid_pos.x + Config.GRID_BORDER - Config.BLOCK_BORDER);
      piece.pos.y =
        dy * Config.BLOCK_SIZE +
        (this.grid_pos.y + Config.GRID_BORDER - Config.BLOCK_BORDER);
    },
    pieceMouseMove: function (e) {
      if (this.clear) return;
      if (this.grabbing == -1) return;
      var piece = this.pieces[this.grabbing];
      piece.pos.x = Math.max(
        0,
        Math.min(
          Config.PUZZLE_HEIGHT - (piece.max_shape.x + 1) * Config.BLOCK_SIZE,
          e.y - this.from_mouse.x + this.from_piece.x
        )
      );
      piece.pos.y = Math.max(
        0,
        Math.min(
          Config.PUZZLE_WIDTH - (piece.max_shape.y + 1) * Config.BLOCK_SIZE,
          e.x - this.from_mouse.y + this.from_piece.y
        )
      );
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
#game {
  width: 100%;
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
  justify-content: center;
  align-items: center;
  position: relative;
}

#puzzle {
  position: relative;
  flex: none;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>
