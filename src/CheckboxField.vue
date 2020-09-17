<template>
    <div :class="{'custom-checkbox': custom, [controlClass]: !!controlClass, [inlineClass]: inline}">
        <input
            :id="$attrs.id || hash"
            ref="field"
            v-bind-events
            v-bind="controlAttributes"
            type="checkbox"
            :value="value"
            :checked="checked || isChecked(value)"
            @input="update">

        <label :for="$attrs.id || hash" :class="{[computedLabelClass]: true, [labelClass]: true}">
            <slot>{{ label }}</slot>
        </label>

        <slot name="feedback">
            <div 
                v-if="invalidFeedback"
                class="invalid-feedback"
                invalid
                v-html="invalidFeedback" />
            <div 
                v-else-if="validFeedback"
                class="valid-feedback"
                valid
                v-html="validFeedback" />
        </slot>

        <slot name="help">
            <small v-if="helpText" ref="help">
                {{ helpText }}
            </small>
        </slot>
    </div>
</template>

<script>
import RadioField from '@vue-interface/radio-field';

export default {

    name: 'CheckboxField',

    extends: RadioField,

    model: {
        prop: 'checkedValues',
        event: 'change'
    },

    props: {

        checked: Boolean,

        /**
         * The checked values
         *
         * @property String
         */
        checkedValues: {
            type: Array,
            default() {
                return [];
            }
        },

        transform: {
            type: Function,
            default: value => value
        },

        /**
         * Determine if the value is checked.
         *
         * @property String
         */
        isChecked:{
            type: Function,
            default(value) {
                if(this.checkedValues.indexOf(value) !== -1) {
                    return true;
                }

                const matches = this.checkedValues.filter(checkedValue => {
                    if(this.compareValues(this.transform(value), checkedValue)) {
                        return true;
                    }
                });
                
                return matches.length > 0;
            }
        },


    },

    methods: {

        stringify(value) {
            try {
                return JSON.stringify(value);
            }
            catch (e) {
                return value;
            }
        },

        compareValues(a, b) {
            if(typeof a === 'object') {
                a = this.stringify(a);
            }
            
            if(typeof b === 'object') {
                b = this.stringify(b);
            }

            return a === b;
        },

        update(event) {
            const value = this.transform(event.target.value);
            const checked = this.checkedValues.slice(0);
            const index = this.checkedValues.findIndex(item => {
                return this.compareValues(value, item);
            });

            if(index === -1) {
                checked.push(value);
            }
            else {
                checked.splice(index, 1);
            }
            
            this.$emit('change', checked);
        }

    }
};
</script>