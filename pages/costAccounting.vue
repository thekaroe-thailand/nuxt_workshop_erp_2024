<script setup>

import axios from 'axios';
import Swal from 'sweetalert2';
import config from '@/config';

definePageMeta({
    layout: 'admin',
});

const products = ref([]);

onMounted(async () => {
    try {
        const response = await axios.get(`${config.apiServer}/api/report/productsAndCost`);
        products.value = response.data.results;

        // คำนวนต้นทุนการผลิตต่อชิ้น
        products.value.forEach((product) => {
            let totalCost = 0;

            for (const material of product.ProductFormular) {
                totalCost += material.quantity * material.Material.price;
            }

            product.totalCost = totalCost;
        });
    } catch (error) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: error.message,
        });
    }
});
</script>

<template>
    <div class="title">ต้นทุนการผลิต</div>
    <div class="p-4">
        <table class="table">
            <thead>
                <tr>
                    <th class="text-left" width="200">ชื่อสินค้า</th>
                    <th class="text-right">ต้นทุนการผลิต</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="product in products" :key="product.id">
                    <td class="flex items-start">{{ product.name }}</td>
                    <td class="text-start">
                        <div v-if="product.ProductFormular.length > 0">
                            <div class="flex justify-between">
                                <div>ส่วนผสม</div>
                                <div class="text-right font-bold mr-1">
                                    {{ product.totalCost.toLocaleString('th-TH') }}
                                </div>
                            </div>
                            <table class="table mt-2">
                                <tbody>
                                    <tr>
                                        <td class="text-left bg-gray-200 font-bold">วัตถุดิบ</td>
                                        <td class="text-right bg-gray-200 font-bold">สัดส่วน</td>
                                        <td class="text-right bg-gray-200 font-bold">ราคา</td>
                                        <td class="text-right bg-gray-200 font-bold">มูลค่าทุน</td>
                                    </tr>
                                    <tr v-for="material in product.ProductFormular" :key="material.id">
                                        <td class="text-left">{{ material.Material.name }}</td>
                                        <td class="text-right">{{ material.quantity }}</td>
                                        <td class="text-right">{{ material.Material.price.toLocaleString('th-TH') }}
                                        </td>
                                        <td class="text-right bg-gray-300 border-t-2 border-gray-100">
                                            {{ (material.quantity * material.Material.price).toLocaleString('th-TH') }}
                                        </td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>