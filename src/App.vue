<template>
    <div id="title-screen" v-if="title">
        <h1 id="title">UNO</h1>
        <button type="button" class="title-button" @click="startGame()">Start Game</button>
        <button type="button" class="title-button">This is just a button</button>
    </div>
    <div id="game-screen">
        <div id="bot-cards" class="cards">
            <button
                v-for="card in botCards"
                :key="card"
                class="card"
                :style="{
                    width: `${99 / this.botCards.length}vw`
                }"
            >
                <span class="uno-text">UNO</span>
            </button>
        </div>
        <img src="/src/assets/BotTurn.png" v-if="turn === 0" id="bot-turn" />
        <img src="/src/assets/PlayerTurn.png" v-else id="player-turn" />
        <button @click="playerDrawTry()" id="draw-pile" class="card">
            <span class="uno-text">UNO</span>
        </button>
        <button
            id="current-card"
            class="card"
            :style="{
                backgroundColor: computeColor(currentCard[0]),
                backgroundImage: computeImage(currentCard[1])
            }"
        >
            <span class="top-text">{{ numToCardStr(currentCard[1]) }}</span>
            <span class="middle-text">{{ numToCardStr(currentCard[1]) }}</span>
            <span class="bottom-text">{{ numToCardStr(currentCard[1]) }}</span>
        </button>
        <div class="card" id="wild-pick" v-if="wild">
            <button type="button" @click="setColor(0)" class="wild-button" id="red">Red</button>
            <button type="button" @click="setColor(1)" class="wild-button" id="yellow">
                Yellow
            </button>
            <button type="button" @click="setColor(2)" class="wild-button" id="green">Green</button>
            <button type="button" @click="setColor(3)" class="wild-button" id="blue">Blue</button>
        </div>
        <div
            id="current-color"
            :style="{
                backgroundColor: computeColor(currentCard[0])
            }"
        ></div>
        <div id="check-uno" @click="checkUno()">!1</div>
        <div id="bot-uno" v-if="botUno">UNO</div>
        <div id="say-uno" @click="sayUno()">1</div>
        <div id="player-uno" v-if="playerUno">UNO</div>
        <div id="player-cards" class="cards">
            <button
                v-for="(card, index) in playerCards"
                @click="playerTry(index)"
                :key="card"
                class="card"
                :style="{
                    width: `${99 / this.playerCards.length}vw`,
                    backgroundColor: computeColor(card[0]),
                    backgroundImage: computeImage(card[1])
                }"
            >
                <span class="top-text">{{ numToCardStr(card[1]) }}</span>
                <span class="middle-text">{{ numToCardStr(card[1]) }}</span>
                <span class="bottom-text">{{ numToCardStr(card[1]) }}</span>
            </button>
        </div>
    </div>
    <div id="end-screen" v-if="winner">
        <span id="player-wins" v-if="winner === 'player'">You Win! :)</span>
        <span id="player-loses" v-if="winner === 'bot'">You Lose! :(</span>
        <button type="button" id="play-again" @click="startGame()">Play Again</button>
    </div>
</template>

<script>
export default {
    name: "App",
    data() {
        return {
            deck: [],
            currentCard: [],
            botCards: [],
            playerCards: [],
            playerDraw: false,
            turn: Math.floor(Math.random() * 2),
            wild: false,
            winner: false,
            botUno: false,
            playerUno: false,
            title: true
        };
    },
    mounted() {
        this.titleScreen();
    },
    methods: {
        resetData() {
            this.deck = [
                [0, 0],
                [0, 1],
                [0, 1],
                [0, 2],
                [0, 2],
                [0, 3],
                [0, 3],
                [0, 4],
                [0, 4],
                [0, 5],
                [0, 5],
                [0, 6],
                [0, 6],
                [0, 7],
                [0, 7],
                [0, 8],
                [0, 8],
                [0, 9],
                [0, 9],
                [0, 10],
                [0, 10],
                [0, 11],
                [0, 11],
                [0, 12],
                [0, 12],
                [1, 0],
                [1, 1],
                [1, 1],
                [1, 2],
                [1, 2],
                [1, 3],
                [1, 3],
                [1, 4],
                [1, 4],
                [1, 5],
                [1, 5],
                [1, 6],
                [1, 6],
                [1, 7],
                [1, 7],
                [1, 8],
                [1, 8],
                [1, 9],
                [1, 9],
                [1, 10],
                [1, 10],
                [1, 11],
                [1, 11],
                [1, 12],
                [1, 12],
                [2, 0],
                [2, 1],
                [2, 1],
                [2, 2],
                [2, 2],
                [2, 3],
                [2, 3],
                [2, 4],
                [2, 4],
                [2, 5],
                [2, 5],
                [2, 6],
                [2, 6],
                [2, 7],
                [2, 7],
                [2, 8],
                [2, 8],
                [2, 9],
                [2, 9],
                [2, 10],
                [2, 10],
                [2, 11],
                [2, 11],
                [2, 12],
                [2, 12],
                [3, 0],
                [3, 1],
                [3, 1],
                [3, 2],
                [3, 2],
                [3, 3],
                [3, 3],
                [3, 4],
                [3, 4],
                [3, 5],
                [3, 5],
                [3, 6],
                [3, 6],
                [3, 7],
                [3, 7],
                [3, 8],
                [3, 8],
                [3, 9],
                [3, 9],
                [3, 10],
                [3, 10],
                [3, 11],
                [3, 11],
                [3, 12],
                [3, 12],
                [4, 13],
                [4, 13],
                [4, 13],
                [4, 13],
                [4, 14],
                [4, 14],
                [4, 14],
                [4, 14]
            ];
            this.currentCard = [];
            this.botCards = [];
            this.playerCards = [];
            this.playerDraw = false;
            this.turn = Math.floor(Math.random() * 2);
            this.wild = false;
            this.winner = false;
            this.botUno = false;
            this.playerUno = false;
        },
        startGame() {
            this.title = false;
            this.resetData();
            let num = Math.floor(Math.random() * this.deck.length);
            this.currentCard = this.deck[num];
            if (this.currentCard[1] >= 10) {
                this.startGame();
                return;
            }
            this.deck.splice(num, 1);
            for (let i = 0; i < 7; i++) {
                this.drawCard(0);
                this.drawCard(1);
            }
            if (this.turn === 0) {
                this.botTurn();
            } else {
                this.playerTurn(true);
            }
        },
        botTurn() {
            this.turn = 0;
            this.botUno = false;
            setTimeout(
                () => {
                    if (
                        1 >= Math.floor(Math.random() * 3) &&
                        this.playerUno === false &&
                        this.playerCards.length === 1
                    ) {
                        this.drawCard(1);
                        this.drawCard(1);
                        this.drawCard(1);
                        this.drawCard(1);
                    }
                    for (let i = 0; i < this.botCards.length; i++) {
                        if (
                            this.botCards[i][0] === this.currentCard[0] ||
                            this.botCards[i][1] === this.currentCard[1] ||
                            this.botCards[i][1] >= 13
                        ) {
                            if (this.botCards[i][1] >= 13) {
                                this.botCards[i][0] = Math.floor(Math.random() * 4);
                            }
                            this.placeCard(0, i);
                            if (this.botCards.length === 1 && 0 === Math.floor(Math.random() * 3)) {
                                this.botUno = true;
                            }
                            if (this.botCards.length === 0) {
                                this.endGame("bot");
                                return;
                            }
                            switch (this.currentCard[1]) {
                                default:
                                    this.playerTurn(true);
                                    break;
                                case 10:
                                    this.botTurn();
                                    break;
                                case 11:
                                    this.drawCard(1);
                                    this.drawCard(1);
                                    this.botTurn();
                                    break;
                                case 12:
                                    this.botTurn();
                                    break;
                                case 14:
                                    this.drawCard(1);
                                    this.drawCard(1);
                                    this.drawCard(1);
                                    this.drawCard(1);
                                    this.botTurn();
                                    break;
                            }
                            return;
                        }
                        this.drawCard(0);
                        console.log(this.botCards);
                        console.log(this.currentCard);
                        setTimeout(
                            () => {
                                for (let i = 0; i < this.botCards.length; i++) {
                                    if (
                                        this.botCards[i][0] === this.currentCard[0] ||
                                        this.botCards[i][1] === this.currentCard[1] ||
                                        this.botCards[i][1] >= 13
                                    ) {
                                        if (this.botCards[i][1] >= 13) {
                                            this.botCards[i][0] = Math.floor(Math.random() * 4);
                                        }
                                        this.placeCard(0, i);
                                        if (
                                            this.botCards.length === 1 &&
                                            0 === Math.floor(Math.random() * 3)
                                        ) {
                                            this.botUno = true;
                                        }
                                        if (this.botCards.length === 0) {
                                            this.endGame("bot");
                                            return;
                                        }
                                        switch (this.currentCard[1]) {
                                            default:
                                                this.turn = 1;
                                                this.playerTurn(true);
                                                break;
                                            case 10:
                                                this.botTurn();
                                                break;
                                            case 11:
                                                this.drawCard(1);
                                                this.drawCard(1);
                                                this.botTurn();
                                                break;
                                            case 12:
                                                this.botTurn();
                                                break;
                                            case 14:
                                                this.drawCard(1);
                                                this.drawCard(1);
                                                this.drawCard(1);
                                                this.drawCard(1);
                                                break;
                                        }
                                        return;
                                    }
                                    this.turn = 1;
                                    this.playerTurn(true);
                                }
                            },
                            Math.random() * 1000 + 500
                        );
                        return;
                    }
                },
                Math.random() * 1000 + 1000
            );
        },
        playerTurn(firstTime) {
            this.turn = 1;
            this.playerUno = false;
            for (let i = 0; i < this.playerCards.length; i++) {
                if (
                    this.playerCards[i][0] === this.currentCard[0] ||
                    this.playerCards[i][1] === this.currentCard[1] ||
                    this.playerCards[i][1] >= 13
                ) {
                    return;
                }
            }
            if (firstTime) {
                this.playerDraw = true;
                console.log("playerdraw");
            } else {
                this.botTurn();
            }
        },
        async playerTry(i) {
            if (this.turn === 1 && this.wild === false) {
                if (
                    this.playerCards[i][0] === this.currentCard[0] ||
                    this.playerCards[i][1] === this.currentCard[1] ||
                    this.playerCards[i][1] >= 13
                ) {
                    if (this.playerCards[i][1] >= 13) {
                        this.wild = true
                        this.playerCards[i][0] = await this.getColor();
                    }
                    this.placeCard(1, i);
                    if (this.playerCards.length === 0) {
                        this.endGame("player");
                        return;
                    }
                    setTimeout(
                        () => {
                            switch (this.currentCard[1]) {
                                default:
                                    this.botTurn();
                                    break;
                                case 10:
                                    this.playerTurn(true);
                                    break;
                                case 11:
                                    this.drawCard(0);
                                    this.drawCard(0);
                                    this.playerTurn(true);
                                    break;
                                case 12:
                                    this.playerTurn(true);
                                    break;
                                case 14:
                                    this.drawCard(0);
                                    this.drawCard(0);
                                    this.drawCard(0);
                                    this.drawCard(0);
                                    break;
                            }
                        },
                        Math.random() * 1000 + 1000
                    );
                }
            }
        },
        playerDrawTry() {
            if (this.playerDraw === true) {
                this.playerDraw = false;
                this.drawCard(1);
                this.playerTurn(false);
            }
        },
        computeColor(color) {
            switch (color) {
                case 0:
                    return "#f25858";
                case 1:
                    return "#edd324";
                case 2:
                    return "#00ad68";
                case 3:
                    return "#006cb4";
            }
        },
        computeImage(number) {
            if (number >= 13) {
                return 'url("/src/assets/WildCardBackground.png")';
            } else {
                return "none";
            }
        },
        drawCard(player) {
            if (this.deck.length !== 0) {
                let num = Math.floor(Math.random() * this.deck.length);
                switch (player) {
                    case 0:
                        this.botCards.push(this.deck[num]);
                        break;
                    case 1:
                        this.playerCards.push(this.deck[num]);
                        break;
                }
                this.deck.splice(num, 1);
            }
        },
        placeCard(player, index) {
            if (this.currentCard[1] >= 13) {
                this.currentCard[0] = 4;
            }
            this.deck.push(this.currentCard);
            switch (player) {
                case 0:
                    this.currentCard = this.botCards[index];
                    this.botCards.splice(index, 1);
                    break;
                case 1:
                    this.currentCard = this.playerCards[index];
                    this.playerCards.splice(index, 1);
                    break;
            }
        },
        numToCardStr(num) {
            switch (num) {
                default:
                    return num;
                case 10:
                    return "⊘";
                case 11:
                    return "❐";
                case 12:
                    return "⮔";
                case 13:
                    return "⊕";
                case 14:
                    return "⛋";
            }
        },
        setColor(color) {
            this.wild = color;
        },
        getColor() {
            return new Promise((resolve) => {
                const unwatch = this.$watch("wild", (newValue) => {
                    if (newValue) {
                        resolve(newValue);
                        unwatch(); // Stop watching once we have a value
                        this.wild = false;
                    }
                });
            });
        },
        checkUno() {
            if (this.botUno === false && this.botCards.length === 1) {
                this.drawCard(0);
                this.drawCard(0);
                this.drawCard(0);
                this.drawCard(0);
            }
        },
        sayUno() {
            if (this.turn === 1 && this.wild === false && this.playerCards.length === 1) {
                this.playerUno = true;
            }
        },
        endGame(winner) {
            this.winner = winner;
        }
    }
};
</script>

<style>
body,
html,
#app {
    margin: 0;
    width: 100vw;
    height: 100vh;
}
#title-screen {
    position: fixed;
    top: 0%;
    bottom: 0%;
    left: 0%;
    right: 0%;
    background-color: #467384;
    z-index: 100;
    display: grid;
    grid-template-rows: 80% 10% 10%;
    grid-template-columns: 100%;
}
#title {
    font-size: 15em;
    color: #fff;
    background-image: url("/src/assets/WildCardBackground.png");
    background-position: center;
    background-size: cover;
    display: grid;
    place-content: center;
    width: 100%;
    height: 100%;
    text-align: center;
}
.title-button {
    border-style: solid;
    border-color: #fff;
    border-width: 5px;
    font-size: 3em;
    color: #fff;
    background-color: #000;
}
#bot-cards {
    position: fixed;
    top: 0%;
    bottom: 70%;
    left: 0%;
    right: 0%;
}
#player-cards {
    position: fixed;
    top: 70%;
    bottom: 0%;
    left: 0%;
    right: 0%;
}
.cards {
    display: flex;
    justify-content: space-around;
    flex-direction: row;
    padding: 0px;
    margin: 0px;
}
.card {
    display: grid;
    grid-template-rows: 20% 60% 20%;
    grid-template-columns: 20% 60% 20%;
    place-content: center;
    height: 100%;
    color: #fff;
    background-color: #000;
    background-position: center;
    background-size: cover;
    box-sizing: border-box;
    padding: 1%;
    border: none;
    border-radius: 2vw;
}
.card > span {
    display: grid;
    place-content: center;
    place-self: center;
}
.top-text {
    font-size: 3em;
    grid-row: 1 / 2;
    grid-column: 1 / 2;
}
.middle-text {
    font-size: 5em;
    grid-row: 2 / 3;
    grid-column: 2 / 3;
}
.bottom-text {
    font-size: 3em;
    grid-row: 3 / 4;
    grid-column: 3 / 4;
}
.uno-text {
    font-size: 3em;
    grid-row: 2 / 3;
    grid-column: 1 / 4;
    background-color: #f00;
    border-radius: 2vw;
    padding: 5px;
}
#bot-turn {
    position: fixed;
    top: 45%;
    bottom: 50%;
    left: 10%;
    right: 80%;
    height: 5%;
}
#player-turn {
    position: fixed;
    top: 50%;
    bottom: 45%;
    left: 10%;
    right: 80%;
    height: 5%;
}
#draw-pile {
    position: fixed;
    top: 35%;
    bottom: 35%;
    left: 30%;
    right: 55%;
    height: 30%;
}
#current-card {
    position: fixed;
    top: 35%;
    bottom: 35%;
    left: 55%;
    right: 30%;
    height: 30%;
}
#wild-pick {
    position: fixed;
    top: 35%;
    bottom: 35%;
    left: 55%;
    right: 30%;
    height: 30%;
    display: grid;
    grid-template-rows: 50% 50%;
    grid-template-columns: 50% 50%;
}
.wild-button {
    width: 90%;
    height: 90%;
    place-self: center;
    border: none;
    border-radius: 2vw;
}
#red {
    background-color: #f25858;
}
#yellow {
    background-color: #edd324;
}
#green {
    background-color: #00ad68;
}
#blue {
    background-color: #006cb4;
}
#current-color {
    position: fixed;
    top: 45%;
    bottom: 45%;
    left: 75%;
    right: 20%;
    border-radius: 2vw;
}
#check-uno {
    background-image: url("/src/assets/WildCardBackground.png");
    background-position: center;
    background-size: cover;
    border-radius: 2vw;
    color: #fff;
    display: grid;
    place-content: center;
    position: fixed;
    top: 35%;
    bottom: 55%;
    left: 85%;
    right: 10%;
}
#bot-uno {
    background-image: url("/src/assets/WildCardBackground.png");
    background-position: center;
    background-size: cover;
    border-radius: 2vw;
    color: #fff;
    display: grid;
    place-content: center;
    position: fixed;
    top: 35%;
    bottom: 55%;
    left: 90%;
    right: 5%;
}
#say-uno {
    background-image: url("/src/assets/WildCardBackground.png");
    background-position: center;
    background-size: cover;
    border-radius: 2vw;
    color: #fff;
    display: grid;
    place-content: center;
    position: fixed;
    top: 55%;
    bottom: 35%;
    left: 85%;
    right: 10%;
}
#player-uno {
    background-image: url("./assets/WildCardBackground.png");
    background-position: center;
    background-size: cover;
    border-radius: 2vw;
    color: #fff;
    display: grid;
    place-content: center;
    position: fixed;
    top: 55%;
    bottom: 35%;
    left: 90%;
    right: 5%;
}
#end-screen {
    background-color: #29434d;
    position: fixed;
    top: 10%;
    bottom: 10%;
    left: 10%;
    right: 10%;
    display: grid;
    grid-template-rows: 50% 50%;
    grid-template-columns: 100%;
    border-radius: 5vw;
}
#player-wins {
    place-self: center;
    color: #00ad68;
    font-size: 10em;
}
#player-loses {
    place-self: center;
    color: #f25858;
    font-size: 10em;
}
#play-again {
    border-radius: 5vw;
    background-color: #5db;
    margin-inline: 10%;
    margin-block: 5%;
    color: #fff;
    font-size: 5em;
    border: none;
}
</style>
