<script setup>
definePageMeta({
    layout: 'admin',
});

const showModal = ref(false);
const materials = ref([
    {
        id: 1,
        name: 'แป้งสาลี',
        remark: '',
        balance: 20,
        unit: 'กิโลกรัม',
        price: 100
    },
    {
        id: 2,
        name: 'น้ำตาล',
        remark: '',
        balance: 10,
        unit: 'กิโลกรัม',
        price: 100
    },
    {
        id: 3,
        name: 'นมผง',
        remark: '',
        balance: 20,
        unit: 'กิโลกรัม',
        price: 100
    }
]);
</script>

<template>
    <div class="title">วัสดุ, ส่วนผสม</div>
    <div class="p-4">
        <button class="btn me-1" @click="showModal = true">
            <i class="fa fa-plus mr-1"></i>
            เพิ่มวัสดุ, ส่วนผสม
        </button>
        <button class="btn me-1">
            <i class="fa fa-list mr-1"></i>
            รายการวัสดุ, ส่วนผสม
        </button>
        <button class="btn me-1">
            <i class="fa fa-arrow-circle-down mr-1"></i>
            รับเข้าสต้องการ
        </button>
        <button class="btn">
            <i class="fa fa-history mr-1"></i>
            ประวัติการรับเข้าสต้องการ
        </button>

        <table class="table mt-3">
            <thead>
                <tr>
                    <th class="text-left">ชื่อ</th>
                    <th class="text-left">หมายเหตุ</th>
                    <th class="text-right">คงเหลือ</th>
                    <th class="text-left">หน่วย</th>
                    <th class="text-right">ราคา</th>
                    <th>วันที่รับเข้าล่าสุด</th>
                    <th width="110px"></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="material in materials" :key="material.id">
                    <td>{{ material.name }}</td>
                    <td>{{ material.remark }}</td>
                    <td class="text-right">{{ material.balance }}</td>
                    <td>{{ material.unit }}</td>
                    <td class="text-right">{{ material.price }}</td>
                    <td>18/10/2024 15:00</td>
                    <td class="text-center">
                        <button class="btn btn-primary me-1">
                            <i class="fa fa-pencil"></i>
                        </button>
                        <button class="btn btn-danger">
                            <i class="fa fa-times"></i>
                        </button>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>
