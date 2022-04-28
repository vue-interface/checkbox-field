<template>
    <div :class="{[controlClass]: !!controlClass, [inlineClass]: inline}">
        <input
            :id="$attrs.id || hash"
            ref="field"
            v-bind-events
            v-bind="controlAttributes"
            type="checkbox"
            :value="value"
            :checked="isChecked(value)"
            @change="update">

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
import { FormControl } from '@vue-interface/form-control';

export default {

    name: 'CheckboxField',

    mixins: [
        FormControl
    ],

    model: {
        prop: 'checkedValues',
        event: 'change'
    },

    props: {
        /**
         * An array of event names that correlate with callback functions
         *
         * @property Function
         */
        bindEvents: {
            type: Array,
            default() {
                return ['focus', 'blur', 'input', 'click', 'keyup', 'keydown', 'progress'];
            }
        },

        /**
         * The checked values
         *
         * @property String
         */
        checked: Boolean,

        /**
         * The checked values.
         *
         * @property String
         */
        checkedValues: {
            type: Array,
            default: () => []
        },

        /**
         * The class name assigned to the control element
         *
         * @property String
         */
        defaultControlClass: {
            type: String,
            default: 'form-check'
        },

        /**
         * Display the form field and label inline
         *
         * @property Function
         */
        inline: Boolean
    },

    // data: () => ({
    //     checkedValues: []
    // }),

    computed: {

        controlClasses() {
            return {
                [this.spacing]: !!this.spacing,
                [this.inputClass]: !!this.inputClass,
                ['is-valid']: !!(this.valid || this.validFeedback),
                ['is-invalid']: !!(this.invalid || this.invalidFeedback),
            };
        },

        computedLabelClass() {
            return `${this.controlClass}-label`;
        },

        hash() {
            return Math.random().toString(20).substr(2, 6);
        },

        inputClass() {
            return `${this.controlClass}-input`;
        },

        inlineClass() {
            return this.inline && `${this.controlClass}-inline`;
        }
    },

    methods: {
        isChecked(value) {
            return Array.isArray(this.checkedValues) && this.checkedValues.indexOf(value) > -1;
        },
        update(event) {
            if(!this.isChecked(event.target.value)) {
                this.checkedValues.push(event.target.value);
            }
            else {
                this.checkedValues.splice(
                    this.checkedValues.indexOf(event.target.value), 1
                );
            }

            this.$emit('change', this.checkedValues);
            this.$emit('input', event);
        }
    }
};
</script>