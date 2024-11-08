<template>
    <div class="wrapper">
        <div class="reset-button">
            <button @click="reset">Reset</button>
        </div>

        <div class="elapsed-time">{{ elapsedTimeFormatted }}</div>

        <div
            class="alert"
            :class="{
                incorrect: !wasLastOperationCorrect,
                correct: wasLastOperationCorrect,
            }"
        >
            {{ wasLastOperationCorrect ? "✅" : "❌" }}
        </div>

        <div class="equation">
            <div>{{ firstNumber }} [] {{ secondNumber }} = {{ result }}</div>
        </div>

        <div class="buttons">
            <button @click="() => checkOperation(Operation.ADD)">+</button>
            <button @click="() => checkOperation(Operation.SUBTRACT)">-</button>
            <button @click="() => checkOperation(Operation.MULTIPLY)">*</button>
            <button @click="() => checkOperation(Operation.DIVIDE)">/</button>
        </div>

        <div class="metrics">
            <div class="metric">Correct count: {{ correctCount }}</div>
            <div class="metric">Correct per second: {{ correctPerSecond.toFixed(2) }}</div>
        </div>
    </div>
</template>

<script setup lang="ts">
    import { ref, computed, onMounted } from "vue";

    enum Operation {
        ADD = "+",
        SUBTRACT = "-",
        MULTIPLY = "*",
        DIVIDE = "/",
    }

    const firstNumber = ref<number>(0);
    const secondNumber = ref<number>(0);
    const operation = ref<Operation | null>(null);
    const wasLastOperationCorrect = ref<boolean>(false);
    const correctCount = ref<number>(0);
    const elapsedTime = ref<number>(0);
    const timer = ref<number | null>(null);

    const elapsedTimeFormatted = computed(() => {
        return new Date(elapsedTime.value * 1000).toISOString().slice(11, 19);
    });

    const result = computed(() => {
        switch (operation.value) {
            case Operation.ADD:
                return firstNumber.value + secondNumber.value;
            case Operation.SUBTRACT:
                return firstNumber.value - secondNumber.value;
            case Operation.MULTIPLY:
                return firstNumber.value * secondNumber.value;
            case Operation.DIVIDE:
                return Math.round((firstNumber.value / secondNumber.value) * 100) / 100;
        }
    });

    const correctPerSecond = computed(() => {
        return correctCount.value / elapsedTime.value;
    });

    function generate() {
        firstNumber.value = Math.floor(Math.random() * 100);
        secondNumber.value = Math.floor(Math.random() * 100);
        operation.value = Object.values(Operation)[Math.floor(Math.random() * 4)];
    }

    function checkOperation(op: Operation) {
        if (op === operation.value) {
            wasLastOperationCorrect.value = true;
            correctCount.value++;
        } else {
            wasLastOperationCorrect.value = false;
        }

        generate();
    }

    function reset() {
        if (timer.value) {
            clearInterval(timer.value);
        }

        elapsedTime.value = 0;
        correctCount.value = 0;
        generate();

        timer.value = setInterval(() => {
            elapsedTime.value++;
        }, 1000);
    }

    onMounted(() => {
        generate();

        timer.value = setInterval(() => {
            elapsedTime.value++;
        }, 1000);
    });
</script>

<style scoped>
    .alert {
        width: 64px;
        height: 64px;

        border-radius: 16px;

        display: flex;
        justify-content: center;
        align-items: center;

        font-size: 2rem;
    }

    .wrapper {
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    .elapsed-time {
        font-size: 1.5rem;
        margin-bottom: 1rem;
    }

    .equation {
        font-size: 5rem;
        margin-bottom: 1rem;
    }

    .buttons {
        display: flex;
        gap: 2rem;
    }

    button {
        min-height: 56px;
        min-width: 64px;
        font-size: 2rem;

        padding: 0 1rem;
        border: none;
        border-radius: 0.5rem;
        background-color: #4caf50;
        color: white;
        cursor: pointer;

        display: flex;
        justify-content: center;
        align-items: center;
    }

    button:hover {
        background-color: #45a049;
    }

    .metrics {
        font-size: 1.5rem;
        margin-top: 1rem;
    }
</style>
