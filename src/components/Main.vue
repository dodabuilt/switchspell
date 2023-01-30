<template>
    <v-row align="center">
        <v-col>
            <v-progress-linear v-model="percentageBar" color="amber" height="25">
                <template v-slot:default="{ value }">
                    <strong>{{ value }}%</strong>
                </template>
            </v-progress-linear>
            <br>
        </v-col>
        <div justify="center">
            <v-rating :model-value="stars" color="amber" density="compact" half-increments readonly
                size="small"></v-rating>
            <div class="text-grey">
                {{ rankValue }} ({{ power }}/{{ possiblePoints }})
            </div>
        </div>
    </v-row>
    <v-card class="mx-auto my-12">
        <v-card-text>
            {{ foundWords }}
        </v-card-text>
    </v-card>
    <v-card class="mx-auto" :class="{ 'apply-shake': shake }" max-width="500">
        <v-card-text>
            <v-row>
                <v-col cols="4">
                    <v-sheet class="pa-2 ma-2">
                        {{ word }}
                    </v-sheet>
                </v-col>
                <!-- <v-col offset="6">
                    <v-sheet class="pa-2 ma-2">
                        <v-btn :disabled="!this.word" color="red" size="medium" variant="text" @click="resetWord">
                            <v-icon icon="mdi-backspace-outline"></v-icon>
                        </v-btn>
                    </v-sheet>
                </v-col> -->
            </v-row>
            <v-divider></v-divider>
            <!-- <div class="pa-1" align="center">
                <v-btn color="yellow-lighten-2" size="large" variant="text" @click="shuffleLetters(this.uniqueLetters)">
                    <v-icon icon="mdi-swap-horizontal-circle-outline"></v-icon>
                </v-btn>
            </div> -->
            <div align="center">
                <v-chip-group class="btn-div pa-3">
                    <v-row>
                        <v-col>
                            <v-chip class="chip" size="large" v-for="letter in uniqueLetters" @click="addToWord(letter)"
                                close>
                                {{ letter }}
                            </v-chip>
                        </v-col>
                    </v-row>
                </v-chip-group>
                <v-btn :disabled="!this.word" size="large" variant="text" @click="resetWord">
                    Delete
                </v-btn>

                <v-btn size="large" variant="text" @click="shuffleLetters(this.uniqueLetters)">
                    <v-icon icon="mdi-swap-horizontal-circle-outline"></v-icon>
                </v-btn>
                <v-btn size="large" variant="text" @click="checkWord">
                    Enter
                </v-btn>
            </div>
        </v-card-text>
    </v-card>
</template>

<script>
export default {
    data() {
        return {
            wordList: null,
            scoreTitle: null,
            score: null,
            foundWords: [],
            letters: [],
            uniqueLetters: [],
            power: 0,
            shake: false,
            possibleWords: [],
            possiblePoints: null,
            word: "",
            points: {
                4: 1,
                5: 3,
                6: 5,
                7: 8,
                8: 10,
                9: 12,
                10: 14,
                11: 20
            }
        }
    },
    created() {
        fetch('../dist/usa.txt')
            .then(response => response.text())
            .then(text => {
                this.wordList = text.split(/\r?\n/);
                this.getRandomWord(text.split(/\r?\n/));
                console.log('random word', this.randomWord)
                this.getPossibleWords(this.letters);
                console.log('all possible words', this.possibleWords)
            })
    },
    computed: {
        percentageBar() {
            return Math.floor((this.power / this.possiblePoints) * 100).toFixed(0)
        },
        stars() {
            return this.percentageBar / 20
        },
        rankValue() {
            if (this.percentageBar <= 20) {
                return 'ur bad'
            } else if (this.percentageBar <= 50) {
                return 'ur better'
            } else if (this.percentageBar <= 75) {
                return 'getting better'
            } else {
                return 'UR A FUCKING LEGEND '
            }

        }
    },
    methods: {
        checkWord() {
            if (this.possibleWords.includes(this.word) && !this.foundWords.includes(this.word)) {
                this.foundWords.push(this.word)
                this.power += this.points[this.word.length]
                this.word = '';
            } else {
                this.shakeAnimation();
                this.word = '';
            }
        },
        shakeAnimation() {
            this.shake = true;
            setTimeout(() => {
                this.shake = false;
            }, 820); // timeout value depending on the duration of the animation
        },

        isPossibleWord(word, letters) {
            var wordLetters = word.split("");
            for (var i = 0; i < wordLetters.length; i++) {
                var index = letters.indexOf(wordLetters[i]);
                if (index === -1) {
                    return false;
                } else {
                    letters = letters.slice(0, index) + letters.slice(index + 1);
                }
            }
            return true;
        },
        findWords(wordList, letters) {
            var words = [];
            for (var i = 0; i < wordList.length; i++) {
                var word = wordList[i];
                if (word.length >= 4 && this.isPossibleWord(word, letters)) {
                    words.push(word);
                }
            }
            return words;
        },
        getPossibleWords(letters) {
            let tmpPoints = 0;
            this.possibleWords = this.findWords(this.wordList, letters.join(''))
            for (let word in this.possibleWords) {
                var wordLength = this.possibleWords[word].length
                tmpPoints += this.points[wordLength]
            }
            this.possiblePoints = tmpPoints;
            console.log('possible points', this.possiblePoints)
        },
        getUniqueLetters(word) {
            var letterSet = new Set();
            for (var i = 0; i < word.length; i++) {
                letterSet.add(word[i]);
            }
            return letterSet
        },
        shuffleLetters(letters) {
            let currentIndex = letters.length, randomIndex;

            // While there remain elements to shuffle.
            while (currentIndex != 0) {

                // Pick a remaining element.
                randomIndex = Math.floor(Math.random() * currentIndex);
                currentIndex--;

                // And swap it with the current element.
                [letters[currentIndex], letters[randomIndex]] = [
                    letters[randomIndex], letters[currentIndex]];
            }

            return letters;
        },
        getRandomWord(list) {
            var randomWord = list[Math.floor(Math.random() * list.length)];
            if (this.getUniqueLetters(randomWord).size !== 7) {
                this.getRandomWord(list)
            } else {
                this.randomWord = randomWord
                this.uniqueLetters = this.shuffleLetters(Array.from(this.getUniqueLetters(randomWord)))
                let letters = [];
                randomWord.split('').forEach(letter => {
                    letters.push(letter);
                });
                this.letters = letters;
            }
        },
        addToWord(letter) {
            this.word += letter
        },
        resetWord() {
            this.word = this.word.slice(0, this.word.length - 1);
        }
    }
}
</script>

<style>
html,
body {
    margin: 0;
    height: 100%;
}

/* Standard syntax */
.btn-div {
    overflow: hidden;
    overflow-x: hidden;
    overflow-y: hidden;
    width: 155px;
}

.chip {
    padding: 5px;
}

.chip:nth-child() {
    padding: 10px;
}

@keyframes shake {

    10%,
    90% {
        transform: translate3d(-1px, 0, 0);
    }

    20%,
    80% {
        transform: translate3d(2px, 0, 0);
    }

    30%,
    50%,
    70% {
        transform: translate3d(-4px, 0, 0);
    }

    40%,
    60% {
        transform: translate3d(4px, 0, 0);
    }
}

.apply-shake {
    animation: shake 0.82s cubic-bezier(0.36, 0.07, 0.19, 0.97) both;
}
</style>
