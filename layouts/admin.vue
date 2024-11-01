<script setup>
</script>

<template>
    <div class="flex">
        <div class="sidebar">
            <Sidebar />
        </div>
        <div class="p-4 w-full bg-gradient-to-b from-gray-500 bg-gray-100">
            <div class="bg-white rounded-xl shadow-lg shadow-gray-500">
                <slot />
            </div>
        </div>
    </div>
</template>