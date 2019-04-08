<template>
    <div>
        <div>
            <input 
                type="text" 
                v-model="exampleText"
            />
            <div>Width: {{width}}</div>

            <input 
                type="range" 
                min="100" max="600"
                v-model="width"
                />
        </div>
        <h2>Simple</h2>
        <svg v-bind:style="{'width': width}">
            <TextItem :width="width" :inner-text="exampleText" />
        </svg>
      </div>
</template>

<script>

import TextItem from './TextItem.vue';

export default {
    name: 'SVGText',
    components: {
        TextItem
    },
    data () {
        return {
            exampleText: 'This is really long text',
            width: 100
        }
    }
}
</script>

<style>
    svg {
        border: 1px solid red;
        font-size: 64px;
        line-height: 56px;
    }

    svg text {
        stroke: black;
        fill: transparent;
    }
</style>
