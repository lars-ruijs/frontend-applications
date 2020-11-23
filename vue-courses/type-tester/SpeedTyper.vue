// What the user sees (the template/component)
<template>
    <h1>Speed Typers!</h1>
    <p>Your score: {{ keywords.filter(keyword => keyword.correct).length }} / {{ keywords.length }}</p>
    <p>
        <span :class="{ correct: keyword.correct, wrong: keyword.wrong, pending: keyword.pending}" :key="keyword.text" v-for="keyword in keywords">{{ keyword.text }}{{ ' ' }}</span>
    </p>
    <input type="text" :value="inputValue" @keyup.space="processInput($event)">
</template>

// The logic where everything happens. 
<script>
const defaultKeywords = ['template', 'data', 'javascript'].map((keyword) => {
    return {text: keyword, correct: false, wrong: false, pending: true}
});

export default {
    data() {
        return {
            inputValue: "",
            index: 0,
            keywords: defaultKeywords,
        };
    },
    methods: {
        processInput(event) {
            const value = event.target.value.trim();

            if (value === "") {
                return;
            }
            else if (this.keywords[this.index].text === value) {
                this.keywords[this.index].correct = true;
                this.keywords[this.index].wrong = false;
                this.keywords[this.index].pending = false;
            }
            else {
                this.keywords[this.index].correct = false;
                this.keywords[this.index].wrong = true;
                this.keywords[this.index].pending = false;
            }
            this.inputValue = ''
            this.index++
        }
    },
};
</script>

// The style section (bound to the component when using scoped)
<style scoped>
    .pending {
        font-weight: bold;
    }
    .correct {
        color: green;
    }
    .wrong {
        color: red;
    }
</style>