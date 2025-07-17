<template>
  <div class="shopping-cart">
    <div class="product-section">
      <h2 class="section-title">商品列表 | 總金額: {{ total }}</h2>
      <ul class="product-list">
        <li v-for="item in items" :key="item.id" class="product-item">
          <div class="product-info">
            <span class="product-name">{{ item.name }}</span>
            <span class="product-price">${{ item.price }}</span>
          </div>
          <button @click="addItemToCart(item)" class="add-to-cart-btn">
            加入購物車
          </button>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

const items = [{ id: 1, name: "商品 A", price: 100 }];

const cart = ref([]);
const total = ref(0);

function add_item(cartArray, item) {
  return [...cartArray, item];
}

function cost_ajax(cart, callback) {
  // 模擬 async API 請求: 小計
  setTimeout(() => {
    const cost = cart.reduce((sum, item) => sum + item.price, 0);
    callback(cost);
  }, 300);
}

function shipping_ajax(cart, callback) {
  // 模擬 async API 請求: 運費
  setTimeout(() => {
    const shipping = cart.length > 0 ? 50 : 0;
    callback(shipping);
  }, 300);
}

function calc_cart_total(cart, callback) {
  let total = 0;
  cost_ajax(cart, function (cost) {
    total += cost;
    shipping_ajax(cart, function (shipping) {
      total += shipping;
      callback(total);
    });
  });
}

function addItemToCart(item) {
  cart.value = add_item(cart.value, item);
  calc_cart_total(cart.value, (newTotal) => {
    total.value = newTotal;
  });
}
</script>

<style scoped>
.shopping-cart {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  font-family: "Arial", sans-serif;
  background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
  border-radius: 15px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
}

.product-section {
  margin-bottom: 30px;
}

.section-title {
  color: #2c3e50;
  font-size: 24px;
  font-weight: 600;
  margin-bottom: 20px;
  text-align: center;
  border-bottom: 3px solid #3498db;
  padding-bottom: 10px;
}

.product-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.product-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  background: white;
  border-radius: 10px;
  padding: 15px 20px;
  margin-bottom: 15px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.08);
  transition:
    transform 0.2s ease,
    box-shadow 0.2s ease;
}

.product-item:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.12);
}

.product-info {
  display: flex;
  flex-direction: column;
  gap: 5px;
}

.product-name {
  font-size: 18px;
  font-weight: 500;
  color: #2c3e50;
}

.product-price {
  font-size: 16px;
  font-weight: 600;
  color: #e74c3c;
}

.add-to-cart-btn {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 25px;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 15px rgba(102, 126, 234, 0.4);
}

.add-to-cart-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(102, 126, 234, 0.6);
}

.add-to-cart-btn:active {
  transform: translateY(0);
}

.total-section {
  background: white;
  border-radius: 15px;
  padding: 25px;
  text-align: center;
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.1);
  border: 2px solid #ecf0f1;
}

.total-display {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 15px;
}

.total-label {
  font-size: 20px;
  font-weight: 500;
  color: #2c3e50;
}

.total-amount {
  font-size: 28px;
  font-weight: 700;
  color: #27ae60;
  background: linear-gradient(135deg, #a8edea 0%, #fed6e3 100%);
  padding: 10px 20px;
  border-radius: 10px;
  border: 2px solid #27ae60;
  min-width: 100px;
  display: inline-block;
}

@media (max-width: 480px) {
  .shopping-cart {
    margin: 10px;
    padding: 15px;
  }

  .product-item {
    flex-direction: column;
    text-align: center;
    gap: 15px;
  }

  .total-display {
    flex-direction: column;
    gap: 10px;
  }

  .total-amount {
    font-size: 24px;
  }
}
</style>
