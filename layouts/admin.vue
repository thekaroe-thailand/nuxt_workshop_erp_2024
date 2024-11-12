<script setup>
</script>

<template>
    <div class="flex">
        <div class="sidebar fixed top-0 left-0 h-full w-64 z-10">
            <Sidebar />
        </div>
        <div class="ml-64 p-4 w-full bg-gradient-to-b from-gray-500 bg-gray-100">
            <div class="bg-white rounded-xl shadow-lg shadow-gray-500">
                <slot />
            </div>
        </div>
    </div>
</template>