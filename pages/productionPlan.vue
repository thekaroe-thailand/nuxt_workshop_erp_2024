<script setup>

import Swal from 'sweetalert2';
import axios from 'axios';
import config from '@/config';

definePageMeta({
    layout: 'admin'
});

const productionPlans = ref([]);
const id = ref('');
const products = ref([]);
const quantity = ref(0);
const remark = ref('');
const showModal = ref(false);
const selectedProductId = ref('');

onMounted(async () => {
    await fetchDataProductionPlan();
});

const openModal = () => {
    showModal.value = true;
    fetchDataProduct();
}

const closeModal = () => {
    quantity.value = 0;
    remark.value = '';

    showModal.value = false;
}

const fetchDataProduct = async () => {
    try {
        if (products.value.length === 0) {
            const response = await axios.get(`${config.apiServer}/api/product/list`);
            products.value = response.data.results;
            selectedProductId.value = products.value[0].id;
        }
    } catch (error) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: error.message
        });
    }
}

const fetchDataProductionPlan = async () => {
    try {
        const response = await axios.get(`${config.apiServer}/api/productionPlan/list`);
        productionPlans.value = response.data.results;
    } catch (error) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: error.message
        });
    }
}

const saveProductionPlan = async () => {
    try {
        const payload = {
            productId: selectedProductId.value,
            quantity: quantity.value,
            remark: remark.value
        }

        if (id.value === '') {
            await axios.post(`${config.apiServer}/api/productionPlan/create`, payload);
        } else {
            await axios.put(`${config.apiServer}/api/productionPlan/update/${id.value}`, payload);
            id.value = '';
        }

        await fetchDataProductionPlan();
        closeModal();
    } catch (error) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: error.message
        });
    }
}

const editProductionPlan = async (productionPlan) => {
    id.value = productionPlan.id;
    selectedProductId.value = productionPlan.productId;
    quantity.value = productionPlan.quantity;
    remark.value = productionPlan.remark;

    openModal();
}

const deleteProductionPlan = async (id) => {
    try {
        const button = await Swal.fire({
            icon: 'warning',
            title: 'ยืนยันการลบ',
            text: 'คุณต้องการลบแผนการผลิตนี้หรือไม่?',
            showCancelButton: true,
            showCancelButton: true
        });

        if (button.isConfirmed) {
            await axios.delete(`${config.apiServer}/api/productionPlan/remove/${id}`);
            await fetchDataProductionPlan();
        }
    } catch (error) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: error.message
        });
    }
}

</script>

<template>
    <div class="title">แผนการผลิต</div>
    <div class="p-4">
        <button class="btn" @click="openModal">
            <i class="fa-solid fa-plus"></i>
            เพิ่มแผนการผลิต
        </button>

        <table class="table mt-3">
            <thead>
                <tr>
                    <th width="200px" class="text-left">สินค้า</th>
                    <th width="220px" class="text-right">จำนวน</th>
                    <th class="text-left">หมายเหตุ</th>
                    <th width="110px"></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="productionPlan in productionPlans" :key="productionPlan.id">
                    <td>{{ productionPlan.Product.name }}</td>
                    <td class="text-right">{{ productionPlan.quantity.toLocaleString('th-TH') }}</td>
                    <td>{{ productionPlan.remark }}</td>
                    <td class="text-center">
                        <button class="btn btn-primary mr-1" @click="editProductionPlan(productionPlan)">
                            <i class="fa-solid fa-pencil"></i>
                        </button>
                        <button class="btn btn-danger" @click="deleteProductionPlan(productionPlan.id)">
                            <i class="fa-solid fa-times"></i>
                        </button>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>

    <Modal v-if="showModal" @close="closeModal" title="แผนการผลิต">
        <div>สินค้า</div>
        <select v-model="selectedProductId" class="form-control">
            <option v-for="product in products" :key="product.id" :value="product.id">
                {{ product.name }}
            </option>
        </select>

        <div class="mt-3">จำนวน</div>
        <input type="number" v-model="quantity" class="form-control" />

        <div class="mt-3">หมายเหตุ</div>
        <input type="text" v-model="remark" class="form-control" />

        <button class="btn mt-3" @click="saveProductionPlan">
            <i class="fa-solid fa-check"></i>
            บันทึก
        </button>
    </Modal>
</template>
