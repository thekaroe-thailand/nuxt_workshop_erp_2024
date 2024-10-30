<script setup>
</script>

<template>
    <div class="flex">
        <div class="sidebar">
            <Sidebar />
        </div>
        <div class="p-4 w-full bg-gray-300">
            <div class="bg-white p-4 rounded-xl shadow-lg">
                <slot />
            </div>
        </div>
    </div>
</template>