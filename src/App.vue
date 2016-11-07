<template>
    <div id="app">
        <img src="./assets/logo.png">
        <h2>The list:</h2>

        <select-dropdown :items="items" @select="selectItem"></select-dropdown>

        <p><strong>Selected:</strong></p>
        <pre>{{ current }}</pre>

    </div>
</template>

<script>
    import SelectDropdown from './SelectDropdown.vue';

    export default {
        name: 'app',
        components: {SelectDropdown},
        data () {
            return {
                names: [
                    "Tiger Nixon",
                    "Garrett Winters",
                    "Ashton Cox",
                    "Cedric Kelly",
                    "Airi Satou",
                    "Brielle Williamson",
                    "Herrod Chandler",
                    "Rhona Davidson",
                    "Colleen Hurst",
                    "Sonya Frost",
                    "Jena Gaines",
                    "Quinn Flynn",
                    "Charde Marshall",
                    "Haley Kennedy",
                    "Tatyana Fitzpatrick",
                    "Michael Silva",
                    "Paul Byrd",
                    "Gloria Little",
                    "Bradley Greer",
                    "Dai Rios",
                    "Jenette Caldwell",
                    "Yuri Berry",
                    "Caesar Vance",
                    "Doris Wilder",
                    "Angelica Ramos",
                    "Gavin Joyce",
                    "Jennifer Chang",
                    "Brenden Wagner",
                    "Fiona Green",
                    "Shou Itou",
                    "Michelle House",
                    "Suki Burks",
                    "Prescott Bartlett",
                    "Gavin Cortez",
                    "Martena Mccray",
                    "Unity Butler",
                ],
                current: null
            }
        },
        computed: {
            items() {
                return this.names.map(n => {
                    return {name: n}
                });
            }
        },
        methods: {
            selectItem(item) {
                console.log(item);
                this.current = item;
            }
        }
    }
</script>

<style>
    * {
        box-sizing: inherit;
    }

    html, body {
        box-sizing: border-box;
    }

    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }

    h1, h2 {
        font-weight: normal;
    }

    ul {
        list-style-type: none;
        padding: 0;
    }

    li {
        display: inline-block;
        margin: 0 10px;
    }

    a {
        color: #42b983;
    }
</style>
