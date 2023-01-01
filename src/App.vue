<template>
  <div>
    <div id="app" class="board">
      <div v-for="(n, index) in boardRows" :key="index">
        <div id="'row_' + index" class="row">
          <div :id="'tile_' + index + '_0'" class="col-xs-3 tile animate__animated"></div>  
          <div :id="'tile_' + index + '_1'" class="col-xs-3 tile animate__animated"></div>  
          <div :id="'tile_' + index + '_2'" class="col-xs-3 tile animate__animated"></div>  
          <div :id="'tile_' + index + '_3'" class="col-xs-3 tile animate__animated"></div>  
          <div :id="'tile_' + index + '_4'" class="col-xs-3 tile animate__animated"></div>
        </div>
      </div>
    </div>
    <div class="row bt">
      <!-- <b-form-textarea v-model="answer" placeholder="Type your word" class="input col-7"></b-form-textarea> -->
      <b-button @click="submitWord()" class="col-3">Solve</b-button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      wordsObj: [],//get from api
      boardRows: 6,
      wordList: [],
      colorsList: [],
      isSolve: false,
      word: "",
      nextWord: false,
      answer: "",
      line: 0,
      info: null
    }
  },
  watch: {
    nextWord: {
      handler(value) {
        if (value) {
          if (this.wordList.length) {
            this.word = this.wordList.pop();
            this.nextWord = false;
            this.handleWord();
          }
        }
      }
    }
  },
  methods: {
    submitWord() {
      fetch("https://localhost:7215/post", {
        method: "POST",
        body: JSON.stringify({
          word: "CLAPS"
        }),
        headers: {
          "Content-Type": "application/json"
        }
      })
        .then(response => response.json())
        .then(data => this.solve(data));
    },
    solve(data) {
      this.wordsObj = data;
      this.wordList = this.wordsObj.map(x => x.guess);
      this.colorsList = this.wordsObj.map(x => x.colors);
      this.word = this.wordList.reverse().pop();
      this.handleWord();
    },
    handleWord() {
      for(let n=0; n < 5; n++) {
        let letter = this.word[n];
        let color = this.colorsList[this.line][n]
        setTimeout(() => {
          let el = document.getElementById(`tile_${this.line}_${n}`);
          el.textContent = letter;
          el.classList.add("animate__flipInX");
          el.style = `background-color:${color};border-color:${color}`;
        },200*n)
      }
      setTimeout(() => {
        this.line++;
        this.nextWord = true;
      }, 1000);
    },
    getLetter(index, position) {
      let letter = this.wordList[index] ? this.wordList[index][position] : '';
      return letter;
    }
  }
}
</script>

<style>
  .board {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    min-width: 360px;
    min-height: 360px;
  }
  .tile {
    justify-content: center;
    align-items: center;
    text-align: center;
    font-size:xx-large;
    height: 52px;
    width: 52px;
    border: 2px solid #3a3a3c;
    margin: 2px;
    color: white;
  }
  .bt {
    justify-content: end;
    align-items: end;
    max-width: 335px;
  }
</style>
