<template>
  <div class="app">
    <div class="correct-boxes">
      <CorrectBox v-for="(connection, index) in connections" :theme="theme(connection)" :key="index" :color="correctBoxColor(connection)" :wordValues="getWordValuePairs(connection)" />
    </div>
    <div class="boxes">
      <Box v-for="(value, key) in boxes" :key="key" :disabled="disabledBoxes" :label="key" :selectedList="selected" @box-clicked="handleBoxClicked" />
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
        boxes: {"Tree": 1, "Red": 2, "Folk": 3, "Yellow": 2,
                "Blue": 2, "Rap": 3, "Jazz": 3, "Sapling": 1,
                "Country": 3, "Forest": 1, "Simple": 4, "Light": 4,
                "Leaf": 1, "Easy": 4, "Green": 2, "Calm": 4
        },
        selected: {},
        guesses: 4,
        connections: [],
        categories: {'con1': "Green", 'con2': "Colors", 'con3': "Music genre", 'con4': "An adjective for something that isn't hard"}
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
        alert("You have ran out of guesses")
        this.selected = {}
        return
      }
      var startValue = Object.entries(this.selected)[0][1];
      for (const [key, value] of Object.entries(this.selected)) {
        if(value != startValue) {
          this.guesses--;
          if (this.guesses == 0) {
            alert("You have ran out of guesses")
            this.selected = {}
          }
          return false;
        }
      }
      var connection = {};
      for (const [key, value] of Object.entries(this.selected)) {
        connection[key] = value;
        delete this.boxes[key];
      }
      this.connections.push(connection);
      this.selected = {};
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

.mistakes {
  margin-top: 2rem
}

</style>