<template>
  <div v-if="gamePhase == 'start'">
    <div id="betAmount">Bet amount:</div>
    <div id="bettingArea">
      <button v-on:click="reduceBet" id="reduceBet">-</button>
      <div id="betDisplay">{{ userBet }}</div>
      <button v-on:click="increaseBet" id="increaseBet">+</button>
    </div>
    <button v-on:click="startGame" id="startGameButton">Start game</button>
  </div>

  <div v-else-if="gamePhase == 'hit'">
    <div class="houseCardValue">House value: {{ houseValue }}</div>
    <div class="houseCards">{{ houseCards }}</div>
    <div class="tableGfx">House must hit on 16 and stand on 17</div>

    <div class="playerCards">{{ playerCards }}</div>
    <div class="playerCardValue">Player value: {{ playerValue }}</div>
    <button v-on:click="gameStand" class="hitStandButton">Stand</button>
    <button v-on:click="gameHit" class="hitStandButton">Hit</button>
    <div id="gameId">game id: {{ gameId }}</div>
  </div>


  <div v-else-if=" gamePhase == 'stand'">
    <div v-if="winner == 'Player'">
      <div class="houseCardValue">House value: {{ houseValue }}</div>
      <div class="houseCards">{{ houseCards }}</div>
      <div class="tableGfx">House must hit on 16 and stand on 17</div>

      <div class="playerCards">{{ playerCards }}</div>
      <div class="playerCardValue">Player value: {{ playerValue }}</div>
      You win
    </div>

    <div v-else-if="winner == 'House'">
      <div class="houseCardValue">House value: {{ houseValue }}</div>
      <div class="houseCards">{{ houseCards }}</div>
      <div class="tableGfx">House must hit on 16 and stand on 17</div>

      <div class="playerCards">{{ playerCards }}</div>
      <div class="playerCardValue">Player value: {{ playerValue }}</div>
      House win
    </div>

    <div v-else-if="winner == 'Push'">
      <div class="houseCardValue">House value: {{ houseValue }}</div>
      <div class="houseCards">{{ houseCards }}</div>
      <div class="tableGfx">House must hit on 16 and stand on 17</div>

      <div class="playerCards">{{ playerCards }}</div>
      <div class="playerCardValue">Player value: {{ playerValue }}</div>
      Push! It's a tie!
    </div>
  </div>
</template>

<script>
export default {
  name: 'GameField',
  data: function () {
    return {
      userId: 1,
      userBet: 10,
      gamePhase: 'start',
      gameId: "",
      houseCards: ["H-4"],
      playerCards: ["H-10", "S-J"],
      playerValue: 20,
      houseValue: 4,
      winner: "0",
      cardsInHand: 0,
      playerHit: true
    }
  },
  methods: {
    reduceBet() {
      if (this.userBet > 10) {
        this.userBet -= 10
      } else {
        alert("Minimum bet is 10")
      }
    },
    increaseBet() {
      if (this.userBet < 50) {
        this.userBet += 10
      } else {
        alert("Maximum bet is 50")
      }
    },
    startGame() {
      this.gamePhase = 'hit'
      let jsonData = {userId: this.userId, userBet: this.userBet}
      let options = {
        method: 'POST',
        mode: 'cors',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(jsonData)
      }
      fetch('http://127.0.0.1:8081/start', options)
          .then(response => response.json())
          .then(data => {
                this.gameId = data.gameId
                this.houseCards = data.houseCards
                this.playerCards = data.playerCards
                this.playerValue = data.playerValue
                this.houseValue = data.houseValue
                this.winner = data.winner
                this.cardsInHand = this.playerCards.length
              }
          )
    },
    gameHit() {
      let jsonData = {gameId: this.gameId, cardsInHand: this.cardsInHand, playerHit: this.playerHit}
      let options = {
        method: 'POST',
        mode: 'cors',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(jsonData)
      }
      fetch('http://127.0.0.1:8081/hit', options)
          .then(response => response.json())
          .then(data => {
                this.gameId = data.gameId
                this.houseCards = data.houseCards
                this.playerCards = data.playerCards
                this.playerValue = data.playerValue
                this.houseValue = data.houseValue
                this.winner = data.winner
                this.cardsInHand++
                if (this.houseCards.length == 2) {
                  this.gamePhase = 'stand'
                }
              }
          )

    },
    gameStand() {
      let jsonData = {gameId: this.gameId, cardsInHand: this.cardsInHand, playerHit: this.playerHit}
      let options = {
        method: 'POST',
        mode: 'cors',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(jsonData)
      }
      fetch('http://127.0.0.1:8081/stand', options)
          .then(response => response.json())
          .then(data => {
                this.houseCards = data.houseCards
                this.playerCards = data.playerCards
                this.playerValue = data.playerValue
                this.houseValue = data.houseValue
                this.winner = data.winner
                this.gamePhase = 'stand'
              }
          )
    }
  },
}
</script>
<style scoped>
#betDisplay, #increaseBet, #reduceBet {
  display: inline;
  margin-left: 3px;
  margin-right: 3px;
}

#startGameButton {
  margin: 10px
}

.tableGfx {
  margin: 10px;
  font-size: 30px;
}

.hitStandButton {
  margin: 3px;
}

#gameId {
  margin-top: 0px;
  margin-bottom: 5px;
}
</style>