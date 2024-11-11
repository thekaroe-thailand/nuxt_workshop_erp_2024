<script setup>

import axios from 'axios'
import config from '@/config'
import Swal from 'sweetalert2'

definePageMeta({
    layout: 'admin',
})

const showModal = ref(false)
const users = ref([])
const id = ref('')
const name = ref('')
const username = ref('')
const password = ref('')
const confirmPassword = ref('')
const level = ref('')
const listLevel = ref(['admin', 'user'])

const fetchData = async () => {
    try {
        const response = await axios.get(`${config.apiServer}/api/users/list`)
        users.value = response.data.results;
    } catch (error) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: error.message,
        })
    }
}

const openModal = () => {
    showModal.value = true
    id.value = ''
    name.value = ''
    username.value = ''
    password.value = ''
    confirmPassword.value = ''
    level.value = 'admin'
}

const closeModal = () => {
    showModal.value = false
}

onMounted(() => {
    fetchData()
})
</script>