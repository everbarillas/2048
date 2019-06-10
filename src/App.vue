<template>
  <div id="app" class="top">

    <nav id="name_section">
      <div class="nav-wrapper white">
        <form>
          <div class="input-field">
            <input id="search_input" type="search" placeholder="Enter your name and press ENTER" v-on:keydown.enter="setName" required>
            <label class="label-icon" for="search_icon"><i class="material-icons"><img id="search_icon" src="images/user.svg" alt="Search icon" width="24" height="24"/></i></label>
          </div>
        </form>
      </div>
    </nav>

    <div id="game" style="display: none;" >
      <div id="name">Name: </div>
      <div id="score">Score: </div>
      <div class="grid"></div>
    </div>

    <!-- Score board-->
    <div id="game_score" style="display: none;" class="row">
      <div class="col s2"></div>
      <div class="col s8">

        <button id="buttonB" type="button" v-on:click="playAgain">Play again</button>

        <table class="striped centered">

          <thead id="head">
          <tr>
            <th>Name</th>
            <th>Score</th>
          </tr>
          </thead>
          <tbody id="body"></tbody>

        </table>

      </div>
      <div class="col s2"></div>
    </div>
  </div>
</template>

<script>
  import firebase from 'firebase/app'
  import 'firebase/firestore'
  // Initialize Firebase
  var config = {
    // Deleted API Key
    apiKey: "",
    authDomain: "project-2617023420537022569.firebaseapp.com",
    databaseURL: "https://project-2617023420537022569.firebaseio.com",
    projectId: "project-2617023420537022569",
    storageBucket: "project-2617023420537022569.appspot.com",
    messagingSenderId: "1025643550970"
  };
  firebase.initializeApp(config);
  const db = firebase.firestore();

  var score = 0;
  var grid_display;
  var gridUpdated = false;
  var nameDB;
  var acceptKey = true;

  export default {
    name: "app",

    // Board with rows, each row has 4 elements set to 0
    data() {
      var grid = [];
      for (let i = 0; i < 4; i++) {
        grid.push([]);
        for (let e = 0; e < 4; e++) {
          grid[i].push({value: 0});
        }
      }
      return { grid }
    },

    mounted: function () {
      const self = this;

      self.generateGrid();
      self.generateRandomNumber();
      self.generateRandomNumber();
      self.keyListener();
    },

    methods: {

      // Set the player's name
      setName: function () {
        event.preventDefault();

        var name = document.querySelector("#search_input").value;
        var nameBoard = document.querySelector("#name");
        nameBoard.innerHTML = "Name: " + name;
        nameDB = name;

        document.querySelector("#name_section").style.display = "none";
        document.querySelector("#game").style.display = "block";

      },

      // Creates Grid UI
      generateGrid() {
        grid_display = document.querySelector(".grid");

        // Adds rows
        for (let i = 0; i < 4; i++) {
          let division = document.createElement("div");
          division.className = "row";

          //  Adds columns to each row
          for (let e = 0; e < 4; e++) {
            let element = document.createElement("div");
            element.id = i + "_" + e;
            element.className = "element";
            element.innerText = 0;
            division.appendChild(element);
          }

          // Add generated elements to div
          grid_display.appendChild(division);
        }
      },

      // Generate number randomly
      generateRandomNumber() {
        const self = this;
        let grid = self.grid;

        // Assign randomTemp a position on the grid and check if said position has 0 as a value
        let randomTemp = grid[Math.floor(Math.random() * 4)][Math.floor(Math.random() * 4)];

        // Loop until the position selected equals 0
        while (randomTemp.value !== 0) {
          randomTemp = grid[Math.floor(Math.random() * 4)][Math.floor(Math.random() * 4)];
        }

        // Once we find a position that is set to 0, we will change its value to 2
        randomTemp.value = 2;

        self.updateUI();
      },

      // Update Grid UI when a key is pressed
      updateUI() {
        const self = this;
        let grid = self.grid;

        for (let i = 0; i < 4; i++) {
          for (let e = 0; e < 4; e++) {
            let board_element = document.getElementById(i + "_" + e);
            board_element.innerText = grid[i][e].value;

            switch (grid[i][e].value) {
              case 0: {
                board_element.style.backgroundColor = "#FFFFFF";
                break;
              }

              case 2: {
                board_element.style.backgroundColor = "#FB9B9A";
                break;
              }

              case 4: {
                board_element.style.backgroundColor = "#F96968";
                break;
              }

              case 8: {
                board_element.style.backgroundColor = "#FCB16D";
                break;
              }

              case 16: {
                board_element.style.backgroundColor = "#FB8B24";
                break;
              }

              case 32: {
                board_element.style.backgroundColor = "#6E6284";
                break;
              }

              case 64: {
                board_element.style.backgroundColor = "#bc90bc";
                break;
              }

              case 128: {
                board_element.style.backgroundColor = "#ff7ca2";
                break;
              }

              case 256: {
                board_element.style.backgroundColor = "#ff6a95";
                break;
              }

              case 512: {
                board_element.style.backgroundColor = "#E5579A";
                break;
              }

              case 1024: {
                board_element.style.backgroundColor = "#D90368";
                break;
              }

              case 2048: {
                board_element.style.backgroundColor = "#7437c4";
                break;
              }
            }
          }
        }
      },

      // Listener for when a key is pressed
      keyListener() {
        const self = this;

        document.addEventListener("keydown", function (event) {
          if (acceptKey) {
            switch (event.which) {
              case 37: {
                self.leftKey();
                break;
              }

              case 38: {
                self.upKey();
                break;
              }

              case 39: {
                self.rightKey();
                break;
              }

              case 40: {
                self.downKey();
                break;
              }
            }

            if (gridUpdated === true) {
              gridUpdated = false;
              self.generateRandomNumber();
            }
          }
        })
      },

      // Function for left movement
      leftKey() {
        const self = this;
        let grid = self.grid;

        for (let i = 0; i < 4; i++) {

          let grid_1 = 1;
          let grid_0 = 0;

          while (grid_1 < 4) {

            if (grid[i][grid_1].value === grid[i][grid_0].value) {
              grid[i][grid_0].value = grid[i][grid_1].value + grid[i][grid_0].value;
              grid[i][grid_1].value = 0;

              self.updateScore(grid[i][grid_0].value);

              gridUpdated = true;

              grid_0++;
              grid_1++;
            } else if ((grid[i][grid_0].value === 0 && grid[i][grid_0].value === 0) || (grid[i][grid_0].value === 0 || (grid[i][grid_1].value != 0 && grid[i][grid_0].value != 0 && (grid_1 - 1 == grid_0)))) {
              grid_0++;
              grid_1++;
            } else if (grid[i][grid_1].value != 0 && grid[i][grid_0].value != 0) {
              grid_0++;
            } else if (grid[i][grid_1].value === 0) {
              grid_1++;
            }
          }

          for (let o = 0; o < 4; o++) {
            for (let u = 0; u < 3; u++) {
              if (grid[i][u].value === 0) {

                let temp = grid[i][u + 1].value;

                grid[i][u + 1].value = 0;

                grid[i][u].value = temp;

                gridUpdated = true;
              }
            }
          }
        }
        self.gameState();
      },

      // Function for up movement
      upKey() {
        const self = this;
        let grid = self.grid;

        for (let i = 0; i < grid.length; i++) {

          let grid_1 = 1;
          let grid_0 = 0;

          while (grid_1 < 4) {

            if (grid[grid_1][i].value === grid[grid_0][i].value) {
              grid[grid_0][i].value = grid[grid_1][i].value + grid[grid_0][i].value;
              grid[grid_1][i].value = 0;

              self.updateScore(grid[grid_0][i].value);

              gridUpdated = true;

              grid_0++;
              grid_1++;
            } else if ((grid[grid_0][i].value === 0 && grid[grid_0][i].value === 0) || (grid[grid_0][i].value === 0 || (grid[grid_1][i].value != 0 && grid[grid_0][i].value != 0 && (grid_1 - 1 == grid_0)))) {
              grid_0++;
              grid_1++;
            } else if (grid[grid_1][i].value != 0 && grid[grid_0][i].value != 0) {
              grid_0++;
            } else if (grid[grid_1][i].value === 0) {
              grid_1++;
            }
          }

          for (let o = 0; o < grid.length; o++) {
            for (let u = 0; u < grid.length - 1; u++) {
              if (grid[u][i].value === 0) {

                let temp = grid[u + 1][i].value;

                grid[u + 1][i].value = 0;

                grid[u][i].value = temp;

                gridUpdated = true;
              }
            }
          }
        }
        self.gameState();
      },

      // Function for right movement
      rightKey() {
        const self = this;
        let grid = self.grid;

        for (let i = 0; i < 4; i++) {
          let grid_2 = 2;
          let grid_3 = 3;

          while (grid_2 >= 0) {

            if (grid[i][grid_2].value === grid[i][grid_3].value) {
              grid[i][grid_3].value = grid[i][grid_2].value + grid[i][grid_3].value;
              grid[i][grid_2].value = 0;

              score += grid[i][grid_3].value;

              self.updateScore(grid[i][grid_3].value);

              gridUpdated = true;

              grid_3--;
              grid_2--;
            } else if ((grid[i][grid_3].value === 0 && grid[i][grid_3].value === 0) || (grid[i][grid_3].value === 0 || (grid[i][grid_2].value != 0 && grid[i][grid_3].value != 0 && (grid_2 + 1 == grid_3)))) {
              grid_3--;
              grid_2--;
            } else if (grid[i][grid_2].value != 0 && grid[i][grid_3].value != 0) {
              grid_3--;
            } else if (grid[i][grid_2].value === 0) {
              grid_2--;
            }
          }

          for (let o = 0; o < 4; o++) {
            for (let u = 3; u > 0; u--) {
              if (grid[i][u].value === 0) {

                let temp = grid[i][u - 1].value;

                grid[i][u - 1].value = 0;

                grid[i][u].value = temp;

                gridUpdated = true;
              }
            }
          }
        }
        self.gameState();
      },

      // Function for down movement
      downKey() {
        const self = this;
        let grid = self.grid;

        for (let i = 0; i < grid.length; i++) {

          let grid_2 = 2;
          let grid_3 = 3;

          while (grid_2 >= 0) {

            if (grid[grid_2][i].value === grid[grid_3][i].value) {
              grid[grid_3][i].value = grid[grid_2][i].value + grid[grid_3][i].value;
              grid[grid_2][i].value = 0;

              self.updateScore(grid[grid_3][i].value);

              gridUpdated = true;

              grid_3--;
              grid_2--;
            } else if ((grid[grid_3][i].value === 0 && grid[grid_3][i].value === 0) || (grid[grid_3][i].value === 0 || (grid[grid_2][i].value != 0 && grid[grid_3][i].value != 0 && (grid_2 + 1 == grid_3)))) {
              grid_3--;
              grid_2--;
            } else if (grid[grid_2][i].value != 0 && grid[grid_3][i].value != 0) {
              grid_3--;
            } else if (grid[grid_2][i].value === 0) {
              grid_2--;
            }
          }

          for (let o = 0; o < grid.length; o++) {
            for (let u = grid.length - 1; u > 0; u--) {
              if (grid[u][i].value === 0) {

                let temp = grid[u - 1][i].value;

                grid[u - 1][i].value = 0;

                grid[u][i].value = temp;

                gridUpdated = true;
              }
            }
          }
        }
        self.gameState();
      },

      // Sets the scoreboard
      generateScoreboard() {
        document.querySelector("#game_score").style.display = "block";
        document.querySelector("#game").style.display = "none";

        var body = document.querySelector("#body");


        db.collection("2048").get().then((snapshot) => {
          snapshot.docs.forEach(doc => {
            var trB = document.createElement("tr");

            var tdName = document.createElement("td");
            tdName.innerText = doc.data().nameG;

            var tdScore = document.createElement("td");
            tdScore.innerText = doc.data().scoreG;

            trB.appendChild(tdName);
            trB.appendChild(tdScore);

            body.appendChild(trB);
          })
        })
      },

      // Updates Score
      updateScore(scoreUpdate) {
        score += scoreUpdate;
        var scoreBoard = document.querySelector("#score");
        scoreBoard.innerHTML = "Score: " + score;
      },

      // Checks if the game is over
      gameState() {
        const self = this;
        if (self.checkGame() === false) {
          acceptKey = false;

          const scoreKeep = {
            nameG: nameDB,
            scoreG: score
          };
          db.collection("2048").add(scoreKeep);
          self.generateScoreboard();
        }
      },

      // Return the state of the game
      checkGame() {
        const self = this;
        let grid = self.grid;

        // If we have a slot that has a 0 the game can continue
        for (let i = 0; i < 4; i++) {
          for (let e = 0; e < 4; e++) {
            if (grid[i][e].value === 0) {
              return true;
            }
          }
        }

        for (let i = 0; i < 3; i++) {
          for (let e = 0; e < 3; e++) {
            // If two row values next to each other have the same value the game can continue
            if (grid[i][e].value === grid[i][e + 1].value) {
              return true;
            }
            // If two column values next to each other have the same value the game can continue
            if (grid[i][e].value === grid[i + 1][e].value) {
              return true;
            }
          }
        }
        return false;
      },

      // Button function to play the game again
      playAgain: function () {
        location.reload();
      }
    }
  }

</script>

<style>

  #buttonB {
    display: flex;
    justify-content: center;

    font-size: 20px;
    color: rgb(38, 20, 71);
    background: antiquewhite;
  }

  #head, #body {
    justify-content: center;
    font-size: 25px;
    color: rgb(38, 20, 71);
  }

  #score, #name {
    justify-content: center;
    display: flex;
    font-size: 3em;
    color: rgb(38, 20, 71);
  }

  .row {
    display: flex;
    justify-content: center;
    font-size: 4em;
  }

  .element {
    width: 2em;
    height: 2em;
    color: rgb(38, 20, 71);
    margin-right: 10px;
    margin-left: 10px;
    background: #FFFFFF;
    display: flex;
    justify-content: center;
    align-items: center;
    border-radius: 5px;
    overflow: hidden;
  }
</style>
