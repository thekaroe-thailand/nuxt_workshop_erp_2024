<script setup>
    const showModal = ref(false);

    definePageMeta({
        layout: 'admin'
    })

    const closeModal = () => {
        showModal.value = false;
    }
</script>

<template>
    <div>ประเภทสินค้า</div>
    <div class="mt-3">
        <button class="btn btn-primary" @click="showModal = true">
            <i class="fa fa-plus"></i>
            เพิ่มประเภทสินค้า
        </button>
    </div>

    <Modal v-if="showModal" title="เพิ่มประเภทสินค้า" @close="closeModal">
        <div>ชื่อ</div>
        <input type="text" class="form-control" />

        <div class="mt-3">หมายเหตุ</div>
        <input type="text" class="form-control" />

        <button class="btn btn-primary mt-3">
            <i class="fa fa-check me-2"></i>
            บันทึก
        </button>
    </Modal>
</template>