<template>
    <text>
        <tspan v-for="(item, index) in lines" x="0" y="1em" v-bind:dy="setDy(index)" :key="index">
            {{item}}
        </tspan>
    </text>
</template>

<script>
export default {
    name: 'TextItem',
    props: {
        width: {
            type: Number | String,
            required: true
        },
        innerText: {
            type: String,
            required: true
        },
    },
    data () {
        return {
            lines: []
        }
    },
    watch: { 
      	width: function(newVal, oldVal) { // watch it
            console.log('Width has changed.. ');
            this.init();
        },
        innerText: function (newVal, oldVal) {
            console.log('Inner Text has changed..');
            this.init();
        }
    },
    mounted () {
        this.init();
        this.$emit('finishedRendering', {
            'lines': this.lines.length
        });
    },
    methods: {
        init: function () {
            const { wordsWithComputedWidth, spaceWidth } = this.calculateWordWidths();
            this.wordsWithComputedWidth = wordsWithComputedWidth;
            this.spaceWidth = spaceWidth;
            
            this.lines = this.calculateLines(this.wordsWithComputedWidth, this.spaceWidth, this.width);
        },
        calculateWordWidths: function () {
            // Calculate length of each word to be used to determine number of words per line
            const words = this.innerText.split(/\s+/);
            var svg = document.createElementNS('http://www.w3.org/2000/svg', 'svg');
            var text = document.createElementNS('http://www.w3.org/2000/svg', 'text');
            //Object.assign(text.style, this.props.style);
            svg.appendChild(text);
            document.body.appendChild(svg);
            
            const wordsWithComputedWidth = words.map( word => {
                text.textContent = word;
                return { word, width: text.getComputedTextLength() }
            });

            text.textContent = '\u00A0'; // Unicode space
            const spaceWidth = text.getComputedTextLength();

            document.body.removeChild(svg);
            
            return { wordsWithComputedWidth, spaceWidth }
        },
        calculateLines: function (wordsWithComputedWidth, spaceWidth, lineWidth) {
            const wordsByLines = wordsWithComputedWidth.reduce((result, { word, width}) => {
                const lastLine = result[result.length - 1] || { words: [], width: 0 };
            
                if (lastLine.words.length === 0) {
                    // First word on line
                    const newLine = { words: [word], width };
                    result.push(newLine);
                } else if (lastLine.width + width + (lastLine.words.length * spaceWidth) < lineWidth) {
                    // Word can be added to an existing line
                    lastLine.words.push(word);
                    lastLine.width += width;
                } else {
                    // Word too long to fit on existing line
                    const newLine = { words: [word], width };
                    result.push(newLine);
                }
            
                return result;
            }, []);
        
            return wordsByLines.map(line => line.words.join(' '));
        },
        setDy: function (index) {
            console.log('computed property called...')
            return `${index}em`;
        },
    },
    computed: {
        
    },
}
</script>

<style>

</style>
