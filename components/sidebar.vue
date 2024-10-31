<script setup>
import { ref } from 'vue';

const activeMenu = ref('');

const toggleMenu = (menu) => {
    activeMenu.value = menu;
}
</script>

<template>
    <div>
        <div class="sidebar-title">NuxtERP V.2024</div>
        <div class="sidebar-avater">
            <img src="https://via.placeholder.com/150" alt="avatar" class="w-10 h-10 rounded-full mx-auto" />
            <div class="text-center text-white text-sm mt-3">Admin System</div>
            <div class="text-center mt-3 flex justify-center gap-2">
                <button class="btn btn-danger text-xs">
                    <i class="fa fa-sign-out mr-1"></i>
                    Sign Out
                </button>
                <button class="btn btn-primary text-xs">
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
            <NuxtLink class="nav-link" :class="{ 'active': activeMenu === 'productType' }" to="/productType"
                @click="toggleMenu('productType')">
                <i class="fa fa-file-alt"></i>
                ประเภทสินค้า
            </NuxtLink>
        </div>
    </div>
</template>