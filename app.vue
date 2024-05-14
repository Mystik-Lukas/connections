<template>
  <div class="app">
    <div class="correct-boxes">
      <CorrectBox v-for="(connection, index) in connections" :theme="theme(connection)" :key="index" :color="correctBoxColor(connection)" :wordValues="getWordValuePairs(connection)" />
    </div>
    <div class="boxes">
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
  </div>
</template>

<script>
import Box from './components/Box.vue';

export default {
  name: "app",
  data() {
    return {
        boxes: {"Mercury": 1, "Athena": 4, "Einstein": 3, "Saturn": 1,
                "Newton": 3, "Hydrogen": 2, "Zeus": 4, "Mars": 1,
                "Carbon": 2, "Poseidon": 4, "Nitrogen": 2, "Oxygen": 2,
                "Galileo": 3, "Jupiter": 1, "Curie": 3, "Apollo": 4
        },
        selected: {},
        guesses: 4,
        connections: [],
        categories: {'con1': "Planets", 'con2': "Elements", 'con3': "Famous Scientists", 'con4': "Greek Mythology"},
        alertVisable: false,
        alertText: ""
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
      console.log(Object.entries(connection).map(([word, value]) => ({ word, value })))
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
  }

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

</style>