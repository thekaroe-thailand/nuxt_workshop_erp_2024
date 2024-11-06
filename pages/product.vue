<script setup>
import Swal from 'sweetalert2';
import axios from 'axios';
import config from '@/config';

definePageMeta({
    layout: 'admin'
});

const showModalAddProduct = ref(false);
const products = ref([]);
const id = ref('');
const name = ref('');
const remark = ref('');

// modal formular
const showModalFormular = ref(false);
const materialId = ref('');
const quantity = ref(0);
const remarkFormular = ref('');
const selectedProductName = ref('');
const selectedProductId = ref('');
const materials = ref([]);
const formulars = ref([]);

const closeModalFormular = () => {
    showModalFormular.value = false;
}

const openModalFormular = async (productId) => {
    showModalFormular.value = true;
    selectedProductName.value = products.value.find(p => p.id === productId).name;
    selectedProductId.value = productId;
    fetchDataFormular(productId);

    if (materials.value.length > 0) return;

    try {
        const response = await axios.get(`${config.apiServer}/api/material/list`);
        materials.value = response.data.results;
        materialId.value = materials.value[0].id;
    } catch (error) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: error.message
        });
    }
}

const fetchDataFormular = async (productId) => {
    try {
        const url = `${config.apiServer}/api/productFormular/list/${productId}`;
        const response = await axios.get(url);
        formulars.value = response.data.results;
    } catch (error) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: error.message
        });
    }
}

const addProduct = async () => {
    showModalAddProduct.value = true;
    id.value = '';
    name.value = '';
    remark.value = '';
}

const closeModalAddProduct = () => {
    showModalAddProduct.value = false;
}

const save = async () => {
    try {
        const payload = {
            name: name.value,
            remark: remark.value
        };

        if (id.value === '') {
            await axios.post(`${config.apiServer}/api/product/create`, payload);
        } else {
            await axios.put(`${config.apiServer}/api/product/update/${id.value}`, payload);
            id.value = '';
        }

        fetchData();
        closeModalAddProduct();
    } catch (error) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: error.message
        });
    }
}

const fetchData = async () => {
    try {
        const response = await axios.get(`${config.apiServer}/api/product/list`);
        products.value = response.data.results;
    } catch (error) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: error.message
        });
    }
}

onMounted(async () => {
    await fetchData();
});

const edit = (product) => {
    id.value = product.id;
    name.value = product.name;
    remark.value = product.remark;

    showModalAddProduct.value = true;
}

const remove = async (id) => {
    try {
        const button = await Swal.fire({
            icon: 'warning',
            title: 'ยืนยันการลบ',
            text: 'คุณต้องการลบสินค้านี้หรือไม่?',
            showCancelButton: true,
            showCancelButton: true
        });

        if (button.isConfirmed) {
            await axios.delete(`${config.apiServer}/api/product/remove/${id}`);
            fetchData();
        }
    } catch (error) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: error.message
        });
    }
}

const saveFormular = async () => {
    try {
        const payload = {
            productId: selectedProductId.value,
            materialId: materialId.value,
            quantity: quantity.value,
            remark: remarkFormular.value
        };

        await axios.post(`${config.apiServer}/api/productFormular/create`, payload);
        await fetchDataFormular(selectedProductId.value);

        quantity.value = 0;
        remarkFormular.value = '';
        materialId.value = materials.value[0].id;
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
    <div class="title">สินค้า</div>
    <div class="p-4">
        <button class="btn" @click="addProduct">
            <i class="fa-solid fa-plus"></i>
            เพิ่มสินค้า
        </button>

        <table class="table mt-3">
            <thead>
                <tr>
                    <th class="text-left" width="200px">ชื่อ</th>
                    <th class="text-left">หมายเหตุ</th>
                    <th width="170px"></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="product in products" :key="product.id">
                    <td class="text-left">{{ product.name }}</td>
                    <td class="text-left">{{ product.remark }}</td>
                    <td class="text-center">
                        <button class="btn mr-1" @click="openModalFormular(product.id)">
                            <i class="fa fa-paperclip"></i>
                            สูตร
                        </button>
                        <button class="btn btn-primary mr-1" @click="edit(product)">
                            <i class="fa-solid fa-pencil"></i>
                        </button>
                        <button class="btn btn-danger" @click="remove(product.id)">
                            <i class="fa-solid fa-times"></i>
                        </button>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>

    <Modal v-if="showModalAddProduct" @close="closeModalAddProduct" title="สินค้า">
        <div>ชื่อ</div>
        <input type="text" v-model="name" class="form-control" />

        <div class="mt-3">หมายเหตุ</div>
        <input type="text" v-model="remark" class="form-control" />

        <button class="btn btn-primary mt-3" @click="save">
            <i class="fa-solid fa-check me-1"></i>
            บันทึก
        </button>
    </Modal>

    <Modal v-if="showModalFormular" @close="closeModalFormular" :title="`สูตรสินค้า : ${selectedProductName}`">
        <div>ส่วนผสม</div>
        <select v-model="materialId" class="form-control">
            <option v-for="material in materials" :key="material.id" :value="material.id">
                {{ material.name }}
            </option>
        </select>

        <div class="mt-3">จำนวน</div>
        <input type="number" v-model="quantity" class="form-control" />

        <div class="mt-3">หมายเหตุ</div>
        <input type="text" v-model="remarkFormular" class="form-control" />

        <button class="btn btn-primary mt-3" @click="saveFormular">
            <i class="fa-solid fa-check me-1"></i>
            บันทึก
        </button>

        <table class="table mt-3" v-if="formulars.length > 0">
            <thead>
                <tr>
                    <th class="text-left">ส่วนผสม</th>
                    <th class="text-right">จำนวน</th>
                    <th class="text-left">หมายเหตุ</th>
                    <th width="110px"></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="formular in formulars" :key="formular.id">
                    <td class="text-left">{{ formular.Material.name }}</td>
                    <td class="text-right">{{ formular.quantity }}</td>
                    <td class="text-left">{{ formular.remark }}</td>
                    <td class="text-center">
                        <button class="btn btn-primary mr-1" @click="editFormular(formular)">
                            <i class="fa-solid fa-pencil"></i>
                        </button>
                        <button class="btn btn-danger" @click="removeFormular(formular.id)">
                            <i class="fa-solid fa-times"></i>
                        </button>
                    </td>
                </tr>
            </tbody>
        </table>
    </Modal>
</template>
