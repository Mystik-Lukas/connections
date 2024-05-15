<template>
  <div class="app">
    <div class="correct-boxes" v-show="!showCorrect">
      <CorrectBox v-for="(connection, index) in connections" :theme="theme(connection)" :key="index" :color="correctBoxColor(connection)" :wordValues="getWordValuePairs(connection)" />
    </div>
    <div class="correct-boxes" v-show="showCorrect">
      <CorrectBox v-for="(connection, index) in correctConnections" :theme="theme(connection)" :key="index" :color="correctBoxColor(connection)" :wordValues="getWordValuePairs(connection)" />
    </div>
    <div class="boxes" v-show="!showCorrect">
      <Box v-for="(value, key) in boxes" :key="key" :disabled="disabledBoxes" :label="key" :selectedList="selected" @box-clicked="handleBoxClicked" />
    </div>
    <div class="alert">
      <Alert v-show="alertVisable" :message="alertText" />
    </div>
    <div class="mistakes">
      <Mistakes :guesses="guesses"/>
    </div>
    <div class="buttons">
      <Button label="Shuffle" :disabled="false" @click="shuffle"/>
      <Button label="Deselect" :disabled="disabledDeselect" @click="deselect"/>
      <Button label="Submit" :disabled="disabledSubmit" @click="submit"/>
    </div>
    <div class="view-answers">
      <Button :label="labelForView" :disabled="false" v-show="guesses == 0" @click="showCorrectToggle"/>
    </div>
  </div>
</template>

<script>
import OpenAI from "openai";
import connectionsData from './connections.json';

export default {
  name: "app",
  data() {
    return {
        boxes: {},
        correctConnections: [],
        selected: {},
        guesses: 4,
        connections: [],
        categories: {},
        alertVisable: false,
        alertText: "",
        labelForView: "View Correct Answers",
        showCorrect: false,
        finished: false
      }
  },
  methods: {
    handleBoxClicked(label) {
      var deleted = false;
      if (this.guesses == 0) {return}
      
      for (const [key, value] of Object.entries(this.boxes)) {
        if (key == label) {
          for (const [key, value] of Object.entries(this.selected)) {
            if (key == label) {
              delete this.selected[key];
              deleted = true;
            }
          }
          if (!deleted) {
            this.selected[key] = value;
          }
        }
      }
    },

    shuffle() {
      const boxArray = Object.entries(this.boxes);

      for (let i = boxArray.length - 1; i > 0; i--) {
        const a = Math.floor(Math.random() * (i + 1));
        [boxArray[i], boxArray[a]] = [boxArray[a], boxArray[i]];
      }

      this.boxes = Object.fromEntries(boxArray);
    },

    deselect() {
      this.selected = {}
    },

    submit() {
      if (this.guesses == 0) {
        this.alertText = "You are out of gueses"
        this.alertVisable = true;
        setTimeout(() => { this.alertVisable = false }, 2000)
        this.selected = {}
        return
      }
      var startValue = Object.entries(this.selected)[0][1];
      var count = 0;
      for (const [key, value] of Object.entries(this.selected)) {
        if(value == startValue) {
          count++
        }
      }
      if(count != 4) {
        this.guesses--;
        if (this.guesses == 0) {
          this.alertText = "You are out of gueses"
          this.alertVisable = true;
          setTimeout(() => { this.alertVisable = false }, 2000)
          this.selected = {}
        } else if (count == 3) {
          this.alertText = "One away"
          this.alertVisable = true;
          setTimeout(() => { this.alertVisable = false }, 2000)
        } else {
          this.alertText = "Incorrect"
          this.alertVisable = true;
          setTimeout(() => { this.alertVisable = false }, 2000)
        }
        return false;
      }
      var connection = {};
      for (const [key, value] of Object.entries(this.selected)) {
        connection[key] = value;
        delete this.boxes[key];
      }
      this.connections.push(connection);
      this.selected = {};
      this.alertText = "Correct!"
      this.alertVisable = true;
      setTimeout(() => { this.alertVisable = false }, 2000)
      return true;
    },

    getWordValuePairs(connection) {
      return Object.entries(connection).map(([word, value]) => ({ word, value }));
    },

    correctBoxColor(connection) {
      var num = Object.entries(connection)[0][1];
      switch(num) {
        case 1: return 'yellow';
        case 2: return 'blue';
        case 3: return 'red';
        case 4: return 'green'
      }
    },

    theme(connection) {
      var num = Object.entries(connection)[0][1];
      switch(num) {
        case 1: return this.categories.con1;
        case 2: return this.categories.con2;
        case 3: return this.categories.con3;
        case 4: return this.categories.con4
      }
    },

    showCorrectToggle() {
      if (this.labelForView == "View Correct Answers") {
        this.labelForView = "View Your Results"
      } else {
        this.labelForView = "View Correct Answers"
      }
      this.showCorrect = !this.showCorrect
    }
  },
  computed: {
    disabledDeselect() {
      return Object.keys(this.selected).length === 0;
    },

    disabledSubmit() {
      return Object.keys(this.selected).length !== 4; 
    },
    numberOfLoops() {
      return Object.entries(this.connections).length/4
    },
    disabledBoxes() {
      return this.guesses == 0;
    }
  },
  async created() {
    const randomConnection = connectionsData[Math.floor(Math.random() * connectionsData.length)].answers
    for (let i = 0; i < 4; i++) {
      let group = randomConnection[i];
      if (group) { // Check if group is defined
        let level = group.level + 1;
        let theme = group.group;

        for (let a = 0; a < 4; a++) {
          this.boxes[group.members[a]] = level;
        }  
      }

      this.categories['con1'] = randomConnection[0].group
      this.categories['con2'] = randomConnection[1].group
      this.categories['con3'] = randomConnection[2].group
      this.categories['con4'] = randomConnection[3].group
      this.finished = true
    }

    var connection1 = {};
      for (const [key, value] of Object.entries(this.boxes).slice(0,4)) {
        connection1[key] = value;
      }
    var connection2 = {};
      for (const [key, value] of Object.entries(this.boxes).slice(4,8)) {
        connection2[key] = value;
    }
    var connection3 = {};
      for (const [key, value] of Object.entries(this.boxes).slice(8,12)) {
        connection3[key] = value;
    }
    var connection4 = {};
      for (const [key, value] of Object.entries(this.boxes).slice(12,16)) {
        connection4[key] = value;
    }
    this.correctConnections.push(connection1)
    this.correctConnections.push(connection2)
    this.correctConnections.push(connection3)
    this.correctConnections.push(connection4)
  },
  mounted() {
    this.shuffle()
  },

};
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap');

.boxes {
  display: grid;
  grid-template-columns: repeat(4, 1fr); 
  gap: 20px; 
  height: fit-content
}

.app {
  display: flex;
  font-family: "Poppins", sans-serif;
  font-weight: 500;
  font-size: 1.2rem;
  color: #4D4D4D;
  height: 100vh;
  flex-direction: column;
  width: fit-content;
  margin-left: auto;
  margin-right: auto;
  gap: .5rem;
  padding: 2rem
}

.buttons {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center
}

.view-answers {
  width: fit-content;
  margin: 1rem auto
}

</style>