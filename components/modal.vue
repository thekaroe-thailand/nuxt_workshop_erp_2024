<script setup>
const emit = defineEmits(['close'])

const props = defineProps({
    title: String,
    size: {
        type: String,
        default: 'md' // for md
    }
});

const modalWidth = computed(() => {
    switch (props.size) {
        case 'lg': return 'max-w-2xl'; // for lg
        case 'xl': return 'max-w-4xl'; // for xl
        case 'md':
        default: return 'max-w-lg'; // for md
    }
});

// close modal
const closeModal = () => {
    emit('close');
}

// esc close modal
const escCloseModal = (event) => {
    if (event.key === 'Escape') {
        closeModal();
    }
}

// check keyboard esc
onMounted(() => {
    window.addEventListener('keydown', escCloseModal);
})
</script>

<template>
    <div class="fixed inset-0 bg-gray-300 bg-opacity-10 flex justify-center items-center shadow-lg">
        <div :class="`w-full ${modalWidth}`">
            <div
                class="modal-title bg-gray-700 text-gray-100 p-4 rounded-tl-xl rounded-tr-xl shadow-lg flex justify-between items-center">
                <span class="text-lg font-bold">{{ title }}</span>
                <button class="btn bg-white text-gray-900 border-2 border-gray-300 shadow-none px-2 py-1"
                    @click="closeModal">
                    <i class="fa fa-times"></i>
                </button>
            </div>
            <div class="modal-body bg-white p-4 rounded-bl-xl rounded-br-xl shadow-lg">
                <slot />
            </div>
        </div>
    </div>
</template>