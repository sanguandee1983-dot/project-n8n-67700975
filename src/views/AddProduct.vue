<template>
  <div class="container py-5">
    <div class="row justify-content-center">
      <div class="col-12 col-md-8 col-lg-6">

        <div class="card shadow-lg border-0 rounded-4">

          <div class="card-header text-center text-white rounded-top-4"
            style="background: linear-gradient(135deg, #198754, #20c997);">
            <h4 class="mb-1 fw-bold">📦 เพิ่มรายการสินค้า</h4>
            <small>กรอกข้อมูลสินค้าเพื่อบันทึกลงระบบ</small>
          </div>

          <div class="card-body p-4">

            <div v-if="status.message" :class="`alert alert-${status.type} alert-dismissible fade show`" role="alert">
              {{ status.message }}
              <button type="button" class="btn-close" @click="status.message = ''"></button>
            </div>

            <form @submit.prevent="submitForm">

              <div class="mb-3">
                <label class="form-label fw-bold">รหัสสินค้า *</label>
                <input type="text" class="form-control" v-model="productData.รหัสสินค้า" placeholder="เช่น P001" required>
              </div>

              <div class="mb-3">
                <label class="form-label fw-bold">ชื่อสินค้า *</label>
                <input type="text" class="form-control" v-model="productData.ชื่อสินค้า" placeholder="ชื่อสินค้าที่ต้องการเพิ่ม" required>
              </div>

              <div class="row">
                <div class="col-md-6 mb-3">
                  <label class="form-label fw-bold">จำนวน *</label>
                  <input type="number" class="form-control" v-model.number="productData.จำนวน" min="1" required>
                </div>
                <div class="col-md-6 mb-3">
                  <label class="form-label fw-bold">ราคา (ต่อชิ้น) *</label>
                  <input type="number" class="form-control" v-model.number="productData.ราคา" min="0" step="0.01" required>
                </div>
              </div>

              <hr class="my-4">

              <button class="btn btn-success w-100 fw-bold py-2 shadow-sm" :disabled="loading">
                <span v-if="loading" class="spinner-border spinner-border-sm me-2"></span>
                {{ loading ? 'กำลังบันทึก...' : '💾 บันทึกข้อมูลสินค้า' }}
              </button>

            </form>
          </div>
        </div>

      </div>
    </div>
  </div>
</template>

<script setup>
import { reactive, ref } from "vue";

// ปรับโครงสร้างข้อมูลให้ตรงกับ JSON ที่ต้องการ
const productData = reactive({
  รหัสสินค้า: "",
  ชื่อสินค้า: "",
  จำนวน: 0,
  ราคา: 0
});

const loading = ref(false);

const status = reactive({
  message: "",
  type: ""
});

const submitForm = async () => {
  loading.value = true;
  status.message = "";

  try {
    const response = await fetch("http://localhost:5678/webhook/product456", { // เปลี่ยน URL ให้ตรงกับ n8n ของคุณ
      method: "POST",
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify({
        ...productData,
        created_at: new Date().toLocaleString('th-TH') // แถม: เพิ่มเวลาที่บันทึก
      })
    });

    if (!response.ok) {
      throw new Error("Network response was not ok");
    }

    status.message = "✅ เพิ่มสินค้าเรียบร้อยแล้ว!";
    status.type = "success";

    // รีเซ็ตฟอร์ม
    productData.รหัสสินค้า = "";
    productData.ชื่อสินค้า = "";
    productData.จำนวน = 0;
    productData.ราคา = 0;

  } catch (error) {
    console.error("Error:", error);
    status.message = "❌ ไม่สามารถบันทึกได้ กรุณาตรวจสอบการเชื่อมต่อ n8n";
    status.type = "danger";
  } finally {
    loading.value = false;
  }
};
</script>

<style scoped>
.card {
  transition: all 0.3s ease-in-out;
}
.card:hover {
  transform: translateY(-8px);
}
.form-control:focus {
  box-shadow: 0 0 0 0.25rem rgba(25, 135, 84, 0.15);
  border-color: #198754;
}
</style>