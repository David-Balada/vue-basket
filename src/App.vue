<script setup>
// Reactivity, Computed, Lifecycle hook
import { ref, computed, onMounted } from "vue";

// components
import Header from "@/components/Header.vue";
import Product from "@/components/Product.vue";

// data
const products = ref([]);

// computed
const numCartItems = computed(() =>
    products.value.reduce((numItems, product) => numItems + product.quantity, 0)
);

const cartTotal = computed(() =>
    products.value.reduce(
        (total, product) => total + product.price * product.quantity,
        0
    )
);

// lifecycle
onMounted(async () => {
  products.value = await fetchProducts();
});

// methods

/**** Async ****/
const removeItem = async ($event) => {
  const productId = $event;

  // update backend
  const response = await fetch(`http://localhost:5000/products/${productId}`, {
    method: "DELETE"
  });

  // update UI
  products.value = products.value.filter((product) => product.id !== productId);
};

const productCount = async ($event, mode) => {
  const productId = $event;
  const productIndex = products.value.findIndex(
      (product) => product.id === productId
  );

  if (mode === 'input') {
    if (products.value[productIndex].quantity === 0) removeItem(productId);
    else {
      const productToUpdate = await fetchProduct(productId);
      const updatedProduct = {
        ...productToUpdate,
        quantity: products.value[productIndex].quantity
      };

      const response = await fetch(
          `http://localhost:5000/products/${productId}`,
          {
            method: "PUT",
            headers: {
              "Content-type": "application/json"
            },
            body: JSON.stringify(updatedProduct)
          }
      );

      products.value[productIndex].quantity = updatedProduct.quantity;
    }
  } else {
    if (products.value[productIndex].quantity === 1 && mode === 'minus') removeItem(productId);
    else {
      const productToUpdate = await fetchProduct(productId);
      const updatedProduct = {
        ...productToUpdate,
        quantity: mode === 'minus' ? --productToUpdate.quantity : ++productToUpdate.quantity
      };

      const response = await fetch(
          `http://localhost:5000/products/${productId}`,
          {
            method: "PUT",
            headers: {
              "Content-type": "application/json"
            },
            body: JSON.stringify(updatedProduct)
          }
      );

      products.value[productIndex].quantity = updatedProduct.quantity;
    }
  }
}

/**** Async ****/
const decrementQuantity = async ($event) => {
  const productId = $event;
  const productIndex = products.value.findIndex(
      (product) => product.id === productId
  );

  // if quantity is 1, remove item
  if (products.value[productIndex].quantity === 1) removeItem(productId);
  else {
    const productToUpdate = await fetchProduct(productId);

    const updatedProduct = {
      ...productToUpdate,
      quantity: --productToUpdate.quantity
    };

    const response = await fetch(
        `http://localhost:5000/products/${productId}`,
        {
          method: "PUT",
          headers: {
            "Content-type": "application/json"
          },
          body: JSON.stringify(updatedProduct)
        }
    );

    products.value[productIndex].quantity = updatedProduct.quantity;
  }
};

/**** Async ****/
const incrementQuantity = async ($event) => {
  const productId = $event;
  const productIndex = products.value.findIndex(
      (product) => product.id === productId
  );

  const productToUpdate = await fetchProduct(productId);
  const updatedProduct = {
    ...productToUpdate,
    quantity: ++productToUpdate.quantity
  };

  const response = await fetch(`http://localhost:5000/products/${productId}`, {
    method: "PUT",
    headers: {
      "Content-type": "application/json"
    },
    body: JSON.stringify(updatedProduct)
  });

  products.value[productIndex].quantity = updatedProduct.quantity;
};

// fetch all products
const fetchProducts = async () => {
  const response = await fetch("http://localhost:5000/products");
  const data = await response.json();
  return data;
};

// fetch single product
const fetchProduct = async (productId) => {
  const response = await fetch(`http://localhost:5000/products/${productId}`);
  const data = await response.json();
  return data;
};
</script>

<template>
  <Header/>
  <!-- cart -->
  <main class="container xl mx-auto p-4">
    <div class="basket-header">
      <div class="cart-header">
        <p>
          <span class="title">Košík</span>
          <span class="items">
              ({{ numCartItems }})
          </span>
        </p>
      </div>
    </div>
    <!-- products -->
    <div class="basket">
      <Product
          v-for="product in products"
          :key="product.id"
          :product="product"
          @remove-item="removeItem"
          @product-count="productCount"
          @decrement-quantity="decrementQuantity"
          @increment-quantity="incrementQuantity"
      />
    </div>
    <!-- footer -->
    <div class="basket-footer">
      <div class="flex items-center justify-end">
        <p>Celkem</p>
        <p class="text-lg ml-2"><strong>{{ cartTotal }} Kč</strong></p>
      </div>
    </div>
  </main>
</template>

<style>
</style>