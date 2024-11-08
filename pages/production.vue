<script setup>

import axios from 'axios';
import config from '@/config';
import Swal from 'sweetalert2';
import dayjs from 'dayjs';

definePageMeta({
    layout: 'admin'
});

const productionPlans = ref([]);
const isShowModal = ref(false);
const productionPlanId = ref('');
const productName = ref('');
const productId = ref('');
const quantity = ref(0);
const remark = ref('');

// modal productions
const isShowModalProduction = ref(false);
const productionId = ref('');
const productionQuantity = ref(0);
const productionRemark = ref('');
const productions = ref([]);
const selectedProductionPlanId = ref('');
const selectedProductName = ref('');

onMounted(() => {
    fetchDataProductionPlan();
});

const openModal = (productionPlan) => {
    isShowModal.value = true;
    productionPlanId.value = productionPlan.id;
    productName.value = productionPlan.Product.name;
    productId.value = productionPlan.Product.id;
};

const closeModal = () => {
    isShowModal.value = false;
};

const fetchDataProductionPlan = async () => {
    try {
        const url = `${config.apiServer}/api/productionPlan/list`;
        const response = await axios.get(url);
        productionPlans.value = response.data.results;

        // sum production amount
        for (const productionPlan of productionPlans.value) {
            let sum = 0;

            for (const production of productionPlan.Production) {
                sum += production.quantity;
            }

            productionPlan.sumAmount = sum;
            productionPlan.sumPercent = (sum / productionPlan.quantity) * 100;
        }
    } catch (e) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: e.message
        });
    }
}

const save = async () => {
    try {
        if (quantity.value == '') {
            Swal.fire({
                icon: 'error',
                title: 'กรุณาระบุจำนวน',
                text: 'กรุณาระบุจำนวนสินค้าที่ผลิต'
            });

            return;
        }

        const payload = {
            productionPlanId: productionPlanId.value,
            quantity: quantity.value,
            remark: remark.value
        };

        await axios.post(`${config.apiServer}/api/production/create`, payload);

        Swal.fire({
            icon: 'success',
            title: 'success',
            text: 'บันทึกข้อมูลสำเร็จ',
            timer: 1000
        });

        closeModal();
        await fetchDataProductionPlan();
    } catch (e) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: e.message
        });
    }
}

const openModalProductions = async (productionPlan) => {
    isShowModalProduction.value = true;
    selectedProductionPlanId.value = productionPlan.id;
    selectedProductName.value = productionPlan.Product.name;

    await fetchDataProductions();
}

const closeModalProductions = () => {
    isShowModalProduction.value = false;
}

const fetchDataProductions = async () => {
    try {
        const url = `${config.apiServer}/api/production/list/${selectedProductionPlanId.value}`;
        const response = await axios.get(url);
        productions.value = response.data.results;
    } catch (e) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: e.message
        });
    }
}

const removeProduction = async (production) => {
    try {
        const button = await Swal.fire({
            icon: 'warning',
            title: 'ยืนยันการลบ',
            text: 'คุณต้องการลบข้อมูลผลิตภัณฑ์นี้หรือไม่?',
            showCancelButton: true,
            showConfirmButton: true
        });

        if (button.isConfirmed) {
            const url = `${config.apiServer}/api/production/remove/${production.id}`;
            await axios.delete(url);
            await fetchDataProductions();
            await fetchDataProductionPlan();
        }
    } catch (e) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: e.message
        });
    }
}
</script>

<template>
    <div class="title">บันทึกการผลิต</div>
    <div class="p-4">
        <table class="table">
            <thead>
                <tr>
                    <th class="text-left">สินค้า</th>
                    <th class="text-left">ประเภท</th>
                    <th class="text-left">บรรจุภัณฑ์</th>
                    <th class="text-right">จำนวน</th>
                    <th class="text-right">ผลิตได้ (ชิ้น)</th>
                    <th class="text-right">ผลิตได้ (%)</th>
                    <th class="text-center">สถานะ</th>
                    <th width="170px"></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="productionPlan in productionPlans" :key="productionPlan.id">
                    <td>
                        <div>{{ productionPlan.Product?.name }}</div>
                        <div v-if="productionPlan.remark" class="text-sm text-red-500">
                            *** {{ productionPlan.remark }}
                        </div>
                    </td>
                    <td>{{ productionPlan.Product.ProductType.name }}</td>
                    <td>{{ productionPlan.Product.Packaging.name }}</td>
                    <td class="text-right">{{ productionPlan.quantity }}</td>
                    <td class="text-right">{{ productionPlan.sumAmount }}</td>
                    <td class="text-right">{{ productionPlan.sumPercent.toFixed(2) }}</td>
                    <td class="text-center">
                        <span v-if="productionPlan.sumPercent < 100" class="text-red-500">
                            <i class="fa fa-check-circle"></i> กำลังผลิต
                        </span>
                        <span v-else style="color: green;">
                            <i class="fa fa-check-circle"></i> ผลิตเสร็จ
                        </span>
                    </td>
                    <td class="text-center">
                        <button class="btn mr-1" @click="openModal(productionPlan)">
                            <i class="fa-solid fa-plus"></i>
                            บันทึกผล
                        </button>
                        <button class="btn btn-primary" @click="openModalProductions(productionPlan)">
                            <i class="fa fa-pencil"></i>
                        </button>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>

    <Modal v-if="isShowModal" @close="closeModal" title="บันทึกผลการผลิต">
        <div>สินค้า</div>
        <div class="bg-gray-200 p-4 rounded-lg">{{ productName }}</div>

        <div class="mt-3">จำนวน</div>
        <input type="number" class="form-control" v-model="quantity" />

        <div class="mt-3">หมายเหตุ</div>
        <input type="text" class="form-control" v-model="remark" />

        <button class="btn mt-3" @click="save">
            <i class="fa fa-check"></i>
            บันทึก
        </button>
    </Modal>

    <Modal v-if="isShowModalProduction" @close="closeModalProductions" title="บันทึกผลการผลิต">
        <div class="mb-3 text-center font-bold text-gray-700">สินค้า {{ selectedProductName }}</div>
        <table class="table">
            <thead>
                <tr>
                    <th class="text-left">วันที่</th>
                    <th class="text-right">จำนวน</th>
                    <th class="text-left">หมายเหตุ</th>
                    <th width="60px"></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="production in productions" :key="production.id">
                    <td class="text-left">{{ dayjs(production.createdAt).format('DD/MM/YYYY HH:mm') }}</td>
                    <td class="text-right">{{ production.quantity }}</td>
                    <td class="text-left">{{ production.remark }}</td>
                    <td class="text-center">
                        <button class="btn btn-danger" @click="removeProduction(production)">
                            <i class="fa fa-times"></i>
                        </button>
                    </td>
                </tr>
            </tbody>
        </table>
    </Modal>
</template>