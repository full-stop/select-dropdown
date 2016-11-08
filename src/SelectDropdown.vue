<template>
    <div class="sd">

        <div class="sd__current" v-if="selected && !visible">
            {{ selected.name }}
        </div>

        <input type="text"
               class="sd__input"
               :placeholder="dynamicPlaceholder"
               v-model="search"
               @input="recalculateIndicated"
               @focus="open"
               @blur="blurClose"
               @keydown="keydown">

        <div class="sd__list" v-show="visible">
            <div v-for="(item, index) in filtered"
                 @click="select(index)"
                 class="sd__item"
                 :class="itemClass(item, index)">
                {{ item.name }}
            </div>
        </div>

    </div>
</template>

<script>
    export default {

        /**
         * Props passed to this component.
         */
        props: {
            /**
             * An array of items is always required. Must be an array of objects with
             * at least a name key. When an item is selected that item will be returned
             * through the "select" and "input" events
             */
            items: {
                type: Array,
                required: true
            },

            /**
             * Optional placeholder used when no item is selected.
             * Comes with a sensible default.
             */
            placeholder: {
                type: String,
                default: 'Choose an item...'
            },

            /**
             * Value property allowing use with v-model.
             * Should be one of the objects in the items array.
             */
            value: Object
        },

        /**
         * Reactive data
         */
        data() {
            return {
                visible: false,
                search: '',
                indicated: null,
                selected: null,
            }
        },

        /**
         * Computed properties
         */
        computed: {

            /**
             * Filters the array of items by the search text input by the user.
             *
             * @return {Array}
             */
            filtered() {
                if (this.search.length) {
                    return this.items.filter(i => i.name.toLowerCase().search(this.search.toLowerCase()) !== -1);
                }

                return this.items;
            },

            /**
             * Decide what to show as the placeholder for the input field.
             * Will show the name of the "indicated" item or the fallback placeholder.
             * If an item is selected no placeholder will be shown.
             *
             * @return {String}
             */
            dynamicPlaceholder() {

                if (this.visible && this.search.length) {
                    return '';
                }

                if (this.visible && this.indicated !== null) {
                    return this.filtered[this.indicated].name;
                }

                return (this.selected === null) ? this.placeholder : '';
            }

        },

        /**
         * Methods
         */
        methods: {

            /**
             * Opens the
             */
            open() {
                if (this.selected !== null) {
                    this.indicated = this.filtered.indexOf(this.selected);
                }
                this.visible = true
            },

            /**
             * Close the list and reset the indicated index.
             */
            close() {
                this.indicated = null;
                this.visible = false;
            },

            /**
             * Special handler for closing the list when the input field is "blurred".
             * We have to set a tiny delay before closing the list if the user wants
             * to select an item, as doing so will trigger the "blur" event, closing
             * the list before the user could actually click an item in the list.
             */
            blurClose() {
                setTimeout(() => this.close(), 100);
            },

            /**
             * Whenever we enter a search term into the input the filtered items change
             * and we need to make sure the index we are "indicating" is still part of
             * the list of filtered items. Easiest way is to set the indicated to 0
             * while there are still items, and null if the search matches nothing.
             */
            recalculateIndicated() {
                this.indicated = this.filtered.length ? 0 : null;
            },

            /**
             * Handle key presses in the input field.
             *
             * @param {KeyboardEvent} e
             */
            keydown(e) {
                switch (e.keyCode)  {
                    case 13: // Enter selects an item
                        if (this.indicated === null) this.indicated = 0;
                        this.select(this.indicated);
                        e.target.blur();
                        break;

                    case 27: // Escape closes the list
                        e.target.blur();
                        break;

                    case 38: // Arrow key down
                        this.indicated = (this.indicated === 0) ? null : this.indicated - 1;
                        break;

                    case 40: // Arrow key up
                        this.indicated = (this.indicated === null) ? 0 : this.indicated + 1;
                        break;
                }
            },

            /**
             * Get the CSS class to apply to the visible items in the list.
             */
            itemClass(item, index) {
                return {
                    'sd__item--indicated': this.indicated === index,
                    'sd__item--selected': this.selected === item
                };
            },

            /**
             * Select an item and close the list.
             * This emits the input event the parent component can listen to,
             * as well as enabling support to use v-model on this component.
             *
             * @param {Number} index
             */
            select(index) {
                if (index < this.filtered.length) {
                    let item = this.filtered[index];
                    this.selected = item;
                    this.$emit('input', item);

                    this.visible = false;
                    this.search = '';
                }
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
    .sd__item--selected,
    .sd__item--indicated {
        background-color: #81a7ff;
        text-shadow: 1px 1px rgba(0,0,0,0.1);
        color: #FFF;
    }

    .sd__item--selected {
        background-color: #3393f4;
    }

</style>