<script setup>
defineProps(["product"]);
defineEmits(["remove-item", "decrement-quantity", "increment-quantity", "product-count"]);
</script>

<template>
  <div class="mb-2.5 ">
    <div class="flex items-center py-4">
      <img :src="product.img" :alt="product.name" class="product-img" />
      <div class="flex items-center flex-row grow">
        <p class="text-lg px-4 py-2">{{ product.name }}</p>
        <p class="px-4 py-2 text-green-600">{{ product.availability }}</p>
        <p class="px-4 py-2">{{ product.price }} Kč</p>
        <p class="px-4 py-2">{{ product.price * product.quantity }} Kč</p>
      </div>
      <div class="w-64 flex items-center flex-row justify-end">
        <div class="flex px-4 py-2">
          <button
              class="decrement-btn p-2 hover:text-red-600"
              @click="$emit('product-count', product.id, 'minus')"
          >
            <i class="fa-solid fa-minus fa-sm"></i>
          </button>
          <input type="number" v-model="product.quantity" @change="$emit('product-count', product.id, 'input')" class="[&::-webkit-inner-spin-button]:appearance-none w-16 text-center leading-8 p-2">
          <button
              class="increment-btn p-2 hover:text-green-600"
              @click="$emit('product-count', product.id, 'plus')"
          >
            <i class="fa-solid fa-plus fa-sm"></i>
          </button>
<!--          <button-->
<!--              class="decrement-btn"-->
<!--              @click="$emit('decrement-quantity', product.id)"-->
<!--          >-->
<!--            <i class="fa-solid fa-minus fa-sm"></i>-->
<!--          </button>-->
<!--          <p class="quantity-num">{{ product.quantity }}</p>-->
<!--          <button-->
<!--              class="increment-btn"-->
<!--              @click="$emit('increment-quantity', product.id)"-->
<!--          >-->
<!--            <i class="fa-solid fa-plus fa-sm"></i>-->
<!--          </button>-->
        </div>
        <button class="ml-4 hover:text-red-600" @click="$emit('remove-item', product.id)">
          <i class="fa-solid fa-trash-can fa-lg"></i>
        </button>
      </div>
    </div>

  </div>
</template>

<style scoped>
</style>
