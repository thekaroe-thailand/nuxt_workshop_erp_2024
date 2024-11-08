<script setup>

import Swal from 'sweetalert2';
import axios from 'axios';
import config from '@/config';

definePageMeta({
    layout: 'admin'
});

const productionPlans = ref([]);
const id = ref('');
const products = ref([]);
const quantity = ref(0);
const remark = ref('');
const showModal = ref(false);
const selectedProductId = ref('');

onMounted(async () => {
    await fetchDataProductionPlan();
});

const openModal = () => {
    showModal.value = true;
    quantity.value = 0;
    remark.value = '';

    fetchDataProduct();
}

const closeModal = () => {
    showModal.value = false;
}

const fetchDataProduct = async () => {
    try {
        if (products.value.length === 0) {
            const response = await axios.get(`${config.apiServer}/api/product/list`);
            products.value = response.data.results;
            selectedProductId.value = products.value[0].id;
        }
    } catch (error) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: error.message
        });
    }
}

const fetchDataProductionPlan = async () => {
    try {
        const response = await axios.get(`${config.apiServer}/api/productionPlan/list`);
        productionPlans.value = response.data.results;
    } catch (error) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: error.message
        });
    }
}

const saveProductionPlan = async () => {
    try {
        const payload = {
            productId: selectedProductId.value,
            quantity: quantity.value,
            remark: remark.value
        }

        if (id.value === '') {
            await axios.post(`${config.apiServer}/api/productionPlan/create`, payload);
        } else {
            await axios.put(`${config.apiServer}/api/productionPlan/update/${id.value}`, payload);
            id.value = '';
        }

        await fetchDataProductionPlan();
        closeModal();
    } catch (error) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: error.message
        });
    }
}


</script>