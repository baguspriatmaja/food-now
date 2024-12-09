<template>
    <div class="bg-images d-flex justify-content-center mb-5">
        <h1 class="font-orange m-auto fw-bold" style="font-size: 50px;">Pizza Order</h1>
    </div>
    <pizza :pizzas="pizzaList" :sizes="sizeList" :toppings="toppingList"></pizza>
</template>

<script setup>
import Pizza from '../pizza/Pizza.vue';
import PIZZA_DATA from '../../pizza.js';
import SIZE_DATA from '../../size.js';
import TOPPING_DATA from '../../topping.js';

const pizzaList = PIZZA_DATA
const sizeList = SIZE_DATA
const toppingList = TOPPING_DATA
</script>

<style scoped>

h1 {
    font-size: 50px;
    color: #e77e23;
}

@media (max-width: 768px) {
    h1 {
        font-size: 30px;
    }
}
</style>
