<template>
    <div class="container-xl d-flex pt-5">
        <div class="content">
            <div class="d-flex justify-content-between">
                <h2 class="fw-bold mb-4" style="color: #e77e23;">Choose your Pizza</h2>
            </div>

            <!-- Pizza List -->
            <div class="d-flex gap-3 me-4">
                <div class="card card-pizza mb-3" v-for="pizza in pizzas" :key="pizza.id" :class="{ 'selected': selectedPizza?.id === pizza.id }" @click="selectPizza(pizza)">
                    <div v-if="pizza.discount.is_active" class="d-flex position-absolute top-0 end-0" style="margin-top: -5px;">
                        <img src="/src/assets/images/ribbon.svg" alt="ribbon" width="80" height="80">
                    </div>
                    <div class="row g-0" style="margin-top: -5px;">
                        <div class="col-md-4">
                        <img :src="pizza.image" class="img-pizza img-fluid rounded-start" :alt="pizza.name">
                        </div>
                        <div class="col-md-8">
                        <div class="card-body text-start">
                            <h5 class="card-title">{{ pizza.name }}</h5>
                            <p class="card-text">
                                <span class="me-2">${{ pizza.discount.final_price.toFixed(2) }}</span>
                                <span v-if="pizza.discount.is_active" class="text-decoration-line-through text-muted">${{ pizza.price.toFixed(2) }}</span>
                            </p>
                        </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Custom Pizza -->
            <h2 class="fw-bold mb-4" style="color: #e77e23; margin-top: 15%;">Custom Pizza</h2>

            <!-- Size -->
            <p class="fw-bold">Size</p>
            <div class="d-flex gap-4 mb-4">
                <div class="form-check" v-for="size in sizes" :key="size.id">
                    <input class="form-check-input custom-radio" type="radio" name="size" :id="'size-' + size.id" :value="size" v-model="selectedSize">
                    <label class="form-check-label" :for="'size-' + size.id">
                        {{ size.name }} (+{{ size.extra_price }}$)
                    </label>
                </div>
            </div>

            <!-- Toppings -->
            <p class="fw-bold">Toppings</p>
            <div class="d-flex gap-3 mb-3 ">
                <div class="button-group" v-for="topping in toppings.slice(0, 6)" :key="topping.id">
                    <input type="checkbox" class="btn-check" :id="topping.id" :value="topping" :disabled="!isToppingAvailable(topping.id)" v-model="selectedToppings">
                    <label class="btn btn-outline-secondary fw-bold" :for="topping.id" :class="{ 'disabled-label': !isToppingAvailable(topping.id) }" style="width: 110px;"> {{ topping.name }} </label>
                </div>
            </div>
            <div class="d-flex gap-3 mb-3 ">
                <div class="button-group" v-for="topping in toppings.slice(6, 13)" :key="topping.id">
                    <input type="checkbox" class="btn-check" :id="topping.id" :value="topping" :disabled="!isToppingAvailable(topping.id)" v-model="selectedToppings">
                    <label class="btn btn-outline-secondary fw-bold" :for="topping.id" :class="{ 'disabled-label': !isToppingAvailable(topping.id) }" style="width: 110px;"> {{ topping.name }} </label>
                </div>
            </div>
        </div>
        
        <!-- Payment Summary -->
        <div class="card ms-5 w-25 shadow p-2 border-0" style="border-radius: 10px; height: fit-content;">
            <div class="card-body">
                <h5 class="card-title fw-bold" style="color: #e77e23;">Payment Summary</h5>
                <div class="container">
                    <div class="row">
                        <!-- Selected Pizza -->
                        <div class="col-6 col-sm-8 mt-2 card-payment">
                            <p>{{ selectedPizza?.name || 'No Pizza Selected' }}</p>
                        </div>
                        <div class="col-6 col-sm-4 mt-2">
                            <p class="text-end">{{ selectedPizza ? '$' + selectedPizza.discount.final_price.toFixed(2) : '' }}</p>
                        </div>

                        <!-- Selected Size -->
                        <div class="col-6 col-sm-8 mt-2 card-payment">
                            <p>{{ selectedSize ? selectedSize.name : 'No size selected' }}</p>
                        </div>
                        <div class="col-6 col-sm-4 mt-2">
                            <p class="text-end">{{ selectedSize ? '$' + selectedSize.extra_price.toFixed(2) : '' }}</p>
                        </div>

                        <!-- Selected Toppings -->
                        <div v-for="topping in selectedToppings" :key="topping.id" class="d-flex">
                            <div class="col-6 col-sm-7 mt-2 card-payment">
                                <p>{{ topping.name }}</p>
                            </div>
                            <div class="col-6 col-sm-5 mt-2">
                                <p class="text-end">{{ '$' + topping.price.toFixed(2)  }}</p>
                            </div>
                        </div>

                        <hr>
                        <div class="d-flex">
                            <div class="col-6 col-sm-8 mt-2 card-payment">
                                <p>Total Price</p>
                            </div>
                            <div class="col-6 col-sm-4 mt-2">
                                <p class="text-end fw-bold" style="color: #e77e23;">${{ calculateTotalPrice().toFixed(2) }}</p>
                            </div>
                        </div>
                    </div>
                </div>
                <button class="btn btn-primary w-100 border-0" style="border-radius: 20px; background-color: #e77e23" @click="openModal"> Order Now </button>
            </div>
        </div>
    </div>

    <!-- Modal Payment -->
    <div v-if="isModalVisible" class="modal-overlay">
        <div class="modal-content">
            <div class="modal-icon">
                <img src="/src/assets/order.gif" alt="Order Modal">
            </div>
                <h3>Order Success</h3>
                <p>Thank you, we have received your order successfully.</p>
            <button class="btn btn-primary" @click="closeModal">Close</button>
        </div>
    </div>


</template>

<script setup>
import { ref } from 'vue';

defineProps({
    pizzas: Array,
    sizes: Array,
    toppings: Array
});

const selectedPizza = ref(null);
const selectedSize = ref(null);
const selectedToppings = ref([]);

function calculateTotalPrice() {
  let total = 0;

  if (selectedPizza.value) {
    total += selectedPizza.value.discount.final_price;
  }

  if (selectedSize.value) {
    total += selectedSize.value.extra_price;
  }

  if (selectedToppings.value.length > 0) {
    total += selectedToppings.value.reduce((sum, topping) => sum + topping.price, 0);
  }   

  return total;
}

function selectPizza(pizza) {
  selectedPizza.value = pizza;
}


function isToppingAvailable(toppingId) {
  if (!selectedPizza.value) return false; 
  return selectedPizza.value.toppings.includes(toppingId);
}

const isModalVisible = ref(false);
function openModal() {
  isModalVisible.value = true;
}
function closeModal() {
  isModalVisible.value = false;

  selectedPizza.value = null;
  selectedSize.value = null;
  selectedToppings.value = [];
}


</script>

<style scoped>

.container-xl {
    flex-wrap: wrap;
}

.content {
    flex: 1;
    min-width: 300px;
}

.card-pizza {
    width: 100%;
    max-width: 100%;
    border-radius: 15px ;
    flex: 1;
    text-align: center;
}

.card-body {
    font-size: 14px;
    
}

.card-payment {
    font-size: 14px;
}

@media (max-width: 768px) {
    .card-pizza {
        max-width: 100px;
    }

    .card-body {
        font-size: 14px;
    }

    .card-payment {
        font-size: 12px;
    }
}


.card-title {
    font-size: 14px;
    font-weight: bold;
}

.card-pizza:hover {
    transform: scale(1.05);
    transition: all 0.3s ease-in-out;
    cursor: pointer;
    background-color: #ffc592;
}

.card-pizza.selected {
  border: 2px solid #e77e23;
  background-color: #e77e23;
}

.img-pizza {
    transition: transform 0.3s ease;
    max-width: 100%;
    max-height: 100%;
}

.card-pizza:hover .img-pizza{
    transform: rotate(15deg);
}   

.custom-radio {
  appearance: none;
  margin-top: 3px;
  width: 20px;
  height: 20px;
  border: 2px solid #ccc;
  border-radius: 50%;
  cursor: pointer;
  position: relative;
  transition: all 0.3s ease;
}

.custom-radio:checked {
  border-color: #e77e23;
  background-color: #e77e23;
}

.custom-radio:checked::after {
  width: 10px;
  height: 10px;
  background-color: white;
  border-radius: 50%;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.btn-check:hover + label {
  border-color: #e77e23;
  color: #e77e23;
}

.btn-check:checked + label {
  background-color: #ffd8b5; 
  color: #e77e23;
  border-color: #e77e23;
}

.disabled-label {
  cursor: not-allowed;
  opacity: 0.5;
}

.btn-check:disabled + label {
  cursor: not-allowed;
  opacity: 0.5;
  background-color: #ccc;
}

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-content {
  background: #fff;
  padding: 20px;
  border-radius: 10px;
  text-align: center;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  width: 300px;
}

.modal-content h3 {
  margin-top: 20px;
  color: #e77e23;
}

.modal-content button {
  margin-top: 20px;
  background: #e77e23;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  color: white;
  cursor: pointer;
  font-size: 14px;
}

.modal-content button:hover {
  background: #cf681c;
}


</style>
