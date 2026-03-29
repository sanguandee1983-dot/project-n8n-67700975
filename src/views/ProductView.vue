<template>
  <div class="container py-5">

    <div class="text-center mb-4">
      <h2 class="fw-bold text-primary">📦 ระบบแสดงรายการสินค้า</h2>
      <p class="text-muted">ข้อมูลจาก n8n Webhook API</p>
    </div>

    <div class="card shadow-lg border-0 rounded-4">
      <div class="card-body">

        <div class="d-flex justify-content-between align-items-center mb-3">
          <h5 class="mb-0">รายการสินค้าในคลัง</h5>
          <button class="btn btn-primary" @click="fetchData">
            🔄 อัปเดตข้อมูล
          </button>
        </div>

        <div v-if="loading" class="text-center my-4">
          <div class="spinner-border text-primary"></div>
          <p class="mt-2">กำลังโหลดข้อมูล...</p>
        </div>

        <div class="table-responsive" v-if="products.length">
          <table class="table table-hover align-middle text-center">
            <thead class="table-primary">
              <tr>
                <th>#</th>
                <th>รหัสสินค้า</th>
                <th>ชื่อสินค้า</th>
                <th>จำนวน</th>
                <th>ราคา (บาท)</th>
                <th>ราคารวม</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(item, index) in products" :key="index">
                <td>{{ index + 1 }}</td>
                <td class="fw-bold text-secondary">{{ item.รหัสสินค้า }}</td>
                <td>{{ item.ชื่อสินค้า }}</td>
                <td>{{ item.จำนวน }}</td>
                <td>{{ item.ราคา }}</td>
                <td class="text-success fw-bold">{{ item.จำนวน * item.ราคา }}</td>
              </tr>
            </tbody>
          </table>
        </div>

        <div v-else-if="!loading" class="text-center text-muted py-4">
          ❌ ไม่พบข้อมูลสินค้า
        </div>

      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

// เปลี่ยนชื่อจาก users เป็น products เพื่อความหมายที่ถูกต้อง
const products = ref([])
const loading = ref(false)

const fetchData = async () => {
  loading.value = true
  try {
    // ตรวจสอบว่า URL ของ n8n ถูกต้องตามที่คุณตั้งค่าไว้
    const response = await fetch('http://localhost:5678/webhook/product123')
    const data = await response.json()
    products.value = data
  } catch (error) {
    console.error('Error fetching products:', error)
  } finally {
    loading.value = false
  }
}

onMounted(() => {
  fetchData()
})
</script>

<style>
body {
  background-color: #f8f9fa;
}
.table-primary {
  --bs-table-bg: #0d6efd;
  --bs-table-color: white;
}
</style>