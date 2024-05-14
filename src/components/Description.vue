<template>
    <div>
        <input type="text" id="description" v-model="text" />
        <p v-if="resultIsBalanced">{{ balancedMessage }}</p>
        <p v-else>{{ unbalancedMessage }}</p>
    </div>
</template>

<script>
import _ from 'lodash'; // Import lodash for debouncing

export default {
    data() {
        return {
            text: '',
            resultIsBalanced: true,
            balancedMessage: 'The text is balanced.',
            unbalancedMessage: 'The text is not balanced.'
        };
    },
    created() {
        // Create a debounced version of the isBalanced method
        this.debouncedIsBalanced = _.debounce(this.isBalanced, 500);
    },
    methods: {
        isBalanced() {
            const textValue = this.text;
            const stack = [];
            const openingChars = '([{';
            const closingChars = ')]}';

            for (let char of textValue) {
                if (openingChars.includes(char)) {
                    stack.push(char);
                } else if (closingChars.includes(char)) {
                    const opening = openingChars[closingChars.indexOf(char)];
                    if (stack.length === 0 || stack.pop() !== opening) {
                        this.resultIsBalanced = false;
                        return;
                    }
                }
            }
            this.resultIsBalanced = stack.length === 0;
        }
    },
    watch: {
        text() {
            // Use the debounced version of isBalanced
            this.debouncedIsBalanced();
        }
    }
};
</script>