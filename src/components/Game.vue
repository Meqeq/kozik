<template>
    <div>
        <div class="settings">
            <input type="text" name="option" @input="set" v-model="name" @blur="setLast" />
            <span>nie ma pleców</span>
        </div>

        <div class="last">
            <div>Ostatnio wybierane</div>
            <div v-for="(element,key) in last" v-bind:key="key" @click="choose(key)">{{element}}</div>
        </div>

        <div class="letters">
            <span v-for="(letter,key) in option" v-bind:key="key" v-bind:class="{ 'sel': key == active}">{{letter}}</span>
        </div>

        <div class="input">
            {{option.charAt(active)}}
            <input type="text" @input="check" name="letter" />
        </div>

        <div class="time">
            Czas: {{(time /1000)}} sek.
        </div>

        <div class="scoreboard">
            <table>
                <thead>
                    <th colspan="2">Najlepsze Wyniki</th>
                </thead>
                <tbody>
                    <tr v-for="(records, key) in scoreBoard" v-bind:key="key" v-bind:class="{ 'best': key == best}">
                        <td>{{records.full}}</td>
                        <td>{{records.fastest/1000}}</td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>

<script>
export default {
    name: 'Game',
    data: () => {
        return {
            option: "niemapleców",
            name: "",
            active: 0,
            start: 0,
            time: 0,
            interval: null,
            scoreBoard: localStorage.getItem("scoreboard") ? JSON.parse(localStorage.getItem("scoreboard")) : [],
            best: -1,
            last: localStorage.getItem("last") ? JSON.parse(localStorage.getItem("last")) : [],
        }
    },
    methods: {
        /**
         * Function checks letter from user input, when letter matches password changes to the next letter
         */
        check: function(e) { 
            let letter = e.target.value.charAt(0).toLowerCase(); // get letter from user input
            if(letter == this.option.toLowerCase().charAt(this.active)){ 
                if( this.active == 0) {
                    this.start = (new Date()).getTime(); 
                    this.interval = setInterval(() => this.add(), 50); // start timer when first letter from input matches first letter from password
                }

                this.active++;
            }

            if(this.active >= this.option.length) { // when whole password was typed
                this.active = 0;
                this.time = (new Date()).getTime() - this.start;
                clearInterval(this.interval);
                
                let scoreJson = localStorage.getItem("scoreboard"); // get scoreboard from localStorage
                let scoreBoard;
                if(scoreJson) { // when scoreboard was saved before
                    scoreBoard = JSON.parse(scoreJson); 

                    let index = scoreBoard.findIndex(value => value.full == this.name + " nie ma pleców"); // find result in scoreboard

                    if(index == -1) { // if not found create new best result
                        scoreBoard.push({
                            full: this.name + " nie ma pleców",
                            fastest: this.time
                        });
                    } else { // if found check if result is better than previous
                        if( this.time < scoreBoard[index].fastest ) {
                            scoreBoard[index].fastest = this.time;
                            this.best = index;
                        }
                    }
                } else { // create new scoreboard
                    scoreBoard = [
                        {
                            full: this.name + " nie ma pleców",
                            fastest: this.time
                        }
                    ]
                }
                this.scoreBoard = scoreBoard;
                localStorage.setItem("scoreboard", JSON.stringify(scoreBoard));
            }

            e.target.value = '';
        }, 
        /**
         * function updates timer
         */
        add: function () {
            this.time = (new Date()).getTime() - this.start;
        }, 
        /**
         * function sets new password to type
         */
        set: function(e) {
            if(!this.active)
                this.option = e.target.value.toLowerCase().replace(" ", "") + "niemapleców";
            else {
                this.name = this.name.substring(0, this.name.length - 1);
            }
        },
        /**
         *  function saves last 3 passwords in localStorage
         */ 
        setLast: function() {
            let lastJson = localStorage.getItem("last");
            let last;
            if(lastJson) {
                last = JSON.parse(lastJson);

                if(last.findIndex(element => { return element == this.name }) == -1) {
                    last.unshift(this.name);
                    if(last.length > 3) {
                        last.pop();
                    }
                }
            } else {
                last = [this.name];
            }
            this.last = last;
            localStorage.setItem("last", JSON.stringify(last));
        }, choose: function(index) {
            this.name = this.last[index];
            this.set({ target: { value: this.name }});
        }
    }
}
</script>

<style scoped>
    .settings {
        padding: 1em;
    }

    .settings input {
        border: 0;
        border-bottom: 0.1em solid #000;
        padding: 0.3em;
        margin-right: 0.5em;
    }

    .letters {
        margin: 1em 0;
        background-color: rgb(214, 214, 214);
        padding: 1em;
    }

    .letters span {
        margin: 0.5em;
        text-align: center;
    }

    .letters .sel {
        text-decoration: underline;
        font-size: 1.2em;
    }

    .input {
        margin: 1em auto;
        width: 3em;
        height: 3em;
        line-height: 3em;
        font-size: 2em;
        border: 0.1em solid #e2e2e2;
        color: #7e7e7e;
        position: relative;
    }

    .input input {
        position: absolute;
        width: 3em;
        height: 3em;
        line-height: 3em;
        top: -0.1em;
        left: -0.1em;
        font-size: 1em;
        border: 0;
        background-color: rgba(0, 0, 0, 0);
        text-align: center;
    }

    .input input:focus {
        animation-name: kek;
        animation-duration: 4s;
        animation-iteration-count: infinite;
        border: 0.2em solid #34aa3a;
        animation-timing-function: ease-in-out;
        animation-direction: alternate;
    }

    @keyframes kek {
        from {
            border-color: #34aa3a;
        }
        to {
            border-color: rgb(55, 64, 179);
        }
    }

    table {
        width: 20em;
        display: inline-table;
        border: 0.1em solid #000;
        border-collapse: collapse;
        margin: 1em;
    }

    td {
        border: 0.1em solid #000;
        padding: 0.5em;
        margin: 0;
    }

    .best {
        animation: dd 0.3s ease-in-out;
    }

    @keyframes dd {
        0% {
            background-color: rgb(25, 163, 83);
        }
        100% {
            background-color: #fff;
        }
    }

    .last {
        background-color: #e2e2e2;
        width: 16em;
        margin: 1em auto;
    }

    .last div:first-child {
        background-color: rgb(150, 150, 150);
        cursor: initial;
    }

    .last div {
        padding: 0.3em;
        cursor: pointer;
    }
</style>