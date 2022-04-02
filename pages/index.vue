<template>
  <div>
    <h1 class="text-xl">Tic Tac Toe</h1>
    <button @click="reset()">Reset</button>

    <div v-if="gameOver" class="text-xl font-black animate-bounce">
      Game Over
    </div>
    <div v-if="winner">
      Winner is
      <span class="animate-pulse">
        {{ winner }}
      </span>
    </div>
    <div class="flex">
      <div class="grid grid-cols-3">
        <div
          v-for="(element, index) in elements"
          :key="index"
          class="w-20 h-20 flex justify-center items-center border border-bg-gray-500"
        >
          <div
            v-if="element !== BLANK"
            class="flex h-full w-full justify-center items-center"
            :class="{
              'bg-green-500': element === X,
              'bg-orange-500': element === O,
            }"
          >
            {{ element }}
          </div>
          <button
            v-else
            class="hover:bg-gray-200 flex h-full w-full justify-center items-center text-gray-200"
            @click="select(index)"
          >
            ❔
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Vue from 'vue'

const X = '❌'
const O = '⭕'
const BLANK = ' '

const COLS = 3
const ROWS = 3
const cols = new Array(COLS).fill().map((_, col) => col)
const rows = new Array(ROWS).fill().map((_, row) => row)

export default {
  name: 'IndexPage',

  data() {
    return {
      elements: [],
      turn: X,
      X,
      O,
      BLANK,
    }
  },
  computed: {
    rows() {
      return rows.map((row) => cols.map((col) => this.boardValue(row, col)))
    },
    cols() {
      return cols.map((col) => rows.map((row) => this.boardValue(row, col)))
    },
    diagonals() {
      const diagonalValues = [
        rows
          .map((row) => [row, cols[row]])
          .map(([row, col]) => this.boardValue(row, col)),
        rows
          .map((row) => [row, cols.reverse()[row]])
          .map(([row, col]) => this.boardValue(row, col)),
      ]
      return diagonalValues
    },
    winner() {
      const winnerInList = (list) =>
        list.reduce((winner, value) => {
          if (winner === undefined) {
            return value
          }
          if (winner === value) {
            return value
          }
          return BLANK
        }, undefined)
      return [...this.rows, ...this.cols, ...this.diagonals]
        .map((row) => winnerInList(row))
        .find((winner) => winner !== BLANK)
    },
    gameOver() {
      const boardFull = this.elements.every((e) => e !== BLANK)
      const hasWinner = this.winner !== undefined
      return boardFull || hasWinner
    },
  },
  watch: {
    gameOver(isOver) {},
  },
  mounted() {
    this.reset()
  },
  methods: {
    boardValue(rowOrIndex, col) {
      if (col !== undefined) {
        const index = ROWS * rowOrIndex + col
        return this.elements[index]
      }
      return this.elements[rowOrIndex]
    },
    changePlayer() {
      if (this.turn === X) {
        this.turn = O
      } else {
        this.turn = X
      }
    },
    reset() {
      this.elements = new Array(ROWS * COLS).fill(BLANK)
      this.turn = X
    },
    select(index) {
      if (this.gameOver) {
        return
      }
      Vue.set(this.elements, index, this.turn)
      this.changePlayer()
    },
  },
}
</script>
