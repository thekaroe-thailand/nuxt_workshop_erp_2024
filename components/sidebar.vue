<script setup>
import { ref } from 'vue';
import Swal from 'sweetalert2';
import config from '@/config';
import axios from 'axios';

const activeMenu = ref('');
const name = ref('');
const level = ref('');

onMounted(async () => {
    fetchData();
});

const fetchData = async () => {
    try {
        const headers = {
            Authorization: `Bearer ${localStorage.getItem(config.token)}`
        };

        const res = await axios.get(`${config.apiServer}/api/user/info`, { headers });
        name.value = res.data.result.name;
        level.value = res.data.result.level;
    } catch (error) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: error.message
        });
    }
}

const toggleMenu = (menu) => {
    activeMenu.value = menu;
}

const signOut = async () => {
    const button = await Swal.fire({
        icon: 'warning',
        title: 'ยืนยันการออกจากระบบ',
        showCancelButton: true,
        showConfirmButton: true,
    });

    if (button.isConfirmed) {
        localStorage.removeItem(config.token);
        localStorage.removeItem('nuxt_erp_user_id');

        navigateTo('/');
    }
}
</script>

<template>
    <div>
        <div class="sidebar-title">NuxtERP V.2024</div>
        <div class="sidebar-avater">
            <div class="text-center">
                <i class="fa fa-user text-2xl text-gray-800 py-2 w-[40px] h-[40px] rounded-lg bg-gray-200"></i>
            </div>
            <div class="text-center text-gray-100 text-md mt-3">{{ name }} : {{ level }}</div>
            <div class="text-center mt-3 flex justify-center gap-2">
                <button class="btn btn-danger text-xs" @click="signOut">
                    <i class="fa fa-sign-out mr-1"></i>
                    Sign Out
                </button>
                <button class="btn btn-primary text-xs" @click="navigateTo('/userProfile')">
                    <i class="fa fa-user mr-1"></i>
                    Profile
                </button>
            </div>
        </div>
        <div class="sidebar-menu">
            <NuxtLink class="nav-link" :class="{ 'active': activeMenu === 'home' }" to="/home"
                @click="toggleMenu('home')">
                <i class="fa fa-home"></i>
                Dashboard
            </NuxtLink>
            <NuxtLink class="nav-link" :class="{ 'active': activeMenu === 'production' }" to="/production"
                @click="toggleMenu('production')">
                <i class="fa fa-copy"></i>
                บันทึกการผลิต
            </NuxtLink>
            <NuxtLink class="nav-link" :class="{ 'active': activeMenu === 'productType' }" to="/productType"
                @click="toggleMenu('productType')">
                <i class="fa fa-file-alt"></i>
                ประเภทสินค้า
            </NuxtLink>
            <NuxtLink class="nav-link" :class="{ 'active': activeMenu === 'product' }" to="/product"
                @click="toggleMenu('product')">
                <i class="fa fa-box"></i>
                สินค้า
            </NuxtLink>
            <NuxtLink class="nav-link" :class="{ 'active': activeMenu === 'material' }" to="/material"
                @click="toggleMenu('material')">
                <i class="fa fa-cogs"></i>
                วัสดุ, ส่วนผสม
            </NuxtLink>
            <NuxtLink class="nav-link" :class="{ 'active': activeMenu === 'packaging' }" to="/packaging"
                @click="toggleMenu('packaging')">
                <i class="fa fa-box"></i>
                บรรจุภัณฑ์
            </NuxtLink>
            <NuxtLink class="nav-link" :class="{ 'active': activeMenu === 'formula' }" to="/formula"
                @click="toggleMenu('formula')">
                <i class="fa fa-receipt"></i>
                สูตรสินค้า
            </NuxtLink>
            <NuxtLink class="nav-link" :class="{ 'active': activeMenu === 'productionPlan' }" to="/productionPlan"
                @click="toggleMenu('productionPlan')">
                <i class="fa fa-calendar"></i>
                แผนการผลิต
            </NuxtLink>
            <NuxtLink class="nav-link" :class="{ 'active': activeMenu === 'report' }" to="/report"
                @click="toggleMenu('report')">
                <i class="fa fa-chart-bar"></i>
                รายงานการผลิต
            </NuxtLink>
            <NuxtLink class="nav-link" :class="{ 'active': activeMenu === 'costAccounting' }" to="/costAccounting"
                @click="toggleMenu('costAccounting')">
                <i class="fa fa-dollar-sign"></i>
                บัญชีต้นทุน
            </NuxtLink>
            <NuxtLink class="nav-link" :class="{ 'active': activeMenu === 'users' }" to="/users"
                @click="toggleMenu('users')">
                <i class="fa fa-users"></i>
                ผู้ใช้งาน
            </NuxtLink>
        </div>
    </div>
</template>