<template>
    <div class="sd">

        <div class="sd__current" v-show="current && !visible">{{ current }}</div>

        <input type="text"
               class="sd__input"
               :placeholder="placeholder"
               v-model="search"
               @focus="open"
               @blur="delayedClose"
               @keydown="keydown">

        <div class="sd__list" v-show="visible">
            <div v-for="(item, index) in filtered"
                 @click="select(index)"
                 class="sd__item"
                 :class="{'sd__item--selected': selected == index}">
                {{ item.name }}
            </div>
        </div>

    </div>
</template>

<script>
    export default {

        props: {
            items: {
                type: Array,
                required: true
            }
        },

        data() {
            return {
                visible: false,
                selected: null,
                search: '',
            }
        },

        computed: {

            filtered() {
                let items = this.items;

                if (this.search.length) {
                    return items.filter(i => i.name.toLowerCase().search(this.search.toLowerCase()) !== -1);
                }

                return items;
            },

            placeholder() {
                return (this.visible || this.selected === null) ? this.current || 'Choose an item' : null;
            },

            current() {
                if (this.selected !== null) {
                    return this.filtered[this.selected].name;
                }
            }

        },

        methods: {

            open() {
                this.visible = true;
            },

            close() {
                this.visible = false;
            },

            delayedClose() {
                setTimeout(() => this.close(), 100);
            },

            keydown(e) {
                switch (e.keyCode)  {
                    case 13: // Enter
                        if (this.selected === null) this.selected = 0;
                        this.select(this.selected);
                        e.target.blur();
                        break;

                    case 27: // Escape
                        e.target.blur();
                        break;


                    case 38: // Arrow key down
                        if (this.selected === 0) this.selected = null;
                        else this.selected--;
                        break;

                    case 40: // Arrow key up
                        if (this.selected === null) this.selected = 0;
                        else if (this.selected < this.filtered.length - 1) this.selected++;
                        break;
                }
            },

            select(index) {
                this.selected = index;
                this.$emit('select', this.filtered[this.selected]);
                this.visible = false;
            }
        }

    }
</script>

<style>

    .sd {
        font-size: 16px;
        position: relative;
        max-width: 500px;
        margin: 0 auto;
    }

    .sd--open {
        box-shadow: 0 3px 5px rgba(0,0,0,.1);
    }

    .sd__input {
        z-index: 200;
        width: 100%;
        display: block;
        height: 40px;
        padding: 20px 15px;
        line-height: 0;
        background-color: #FFF;
        border: 1px solid #DDD;
    }

    .sd__current {
        position: absolute;
        left: 15px;
        top: 10px;
        padding: 12px 0;
        line-height: 0;
        pointer-events: none;
        font-weight: 600;
    }

    .sd__list {
        position: absolute;
        z-index: 100;
        max-height: 300px;
        overflow-y: scroll;
        overflow-x: hidden;
        top: 42px;
        left: 0;
        right: 0;
        background-color: transparent;
        box-shadow: 0 2px 8px rgba(0,0,0,.1);
    }

    .sd__item {
        padding: 5px 10px;
        line-height: 25px;
        border: 1px solid #DDD;
        border-top: none;
        cursor: pointer;
        background-color: #FFF;
    }

    .sd__item:hover,
    .sd__item--selected {
        background-color: #3393f4;
        text-shadow: 1px 1px rgba(0,0,0,0.1);
        color: #FFF;
    }

</style>