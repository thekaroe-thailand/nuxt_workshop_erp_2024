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
        const response = await axios.get(`${config.apiServer}/api/user/list`)
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

const save = async () => {
    if (password.value !== confirmPassword.value) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: 'Password ไม่ตรงกัน',
        })
        return;
    }

    try {
        const payload = {
            name: name.value,
            username: username.value,
            password: password.value,
            level: level.value,
        }

        if (id.value === '') {
            await axios.post(`${config.apiServer}/api/user/create`, payload)
        } else {
            await axios.put(`${config.apiServer}/api/user/updateUser/${id.value}`, payload)
            id.value = ''
        }

        fetchData()
        closeModal()
    } catch (error) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: error.message,
        })
    }
}

const edit = (user) => {
    id.value = user.id
    name.value = user.name
    username.value = user.username
    level.value = user.level
    showModal.value = true
}

const remove = async (id) => {
    try {
        const button = await Swal.fire({
            icon: 'warning',
            title: 'ยืนยันการลบ',
            text: 'คุณต้องการลบข้อมูลนี้หรือไม่',
            showCancelButton: true,
            showConfirmButton: true,
        })

        if (button.isConfirmed) {
            await axios.delete(`${config.apiServer}/api/user/remove/${id}`)
            fetchData()
        }
    } catch (error) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: error.message,
        })
    }
}
</script>

<template>
    <div class="title">ผู้ใช้งาน</div>
    <div class="p-4">
        <button class="btn mb-4" @click="openModal">
            <i class="fa-solid fa-plus"></i>
            เพิ่มผู้ใช้งาน
        </button>

        <table class="table">
            <thead>
                <tr>
                    <th class="text-left">ชื่อ</th>
                    <th class="text-left">username</th>
                    <th class="text-left">ระดับสิทธิ์</th>
                    <th width="110px"></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="user in users" :key="user.id">
                    <td>{{ user.name }}</td>
                    <td>{{ user.username }}</td>
                    <td>{{ user.level }}</td>
                    <td class="text-center">
                        <button class="btn btn-primary mr-1" @click="edit(user)">
                            <i class="fa-solid fa-pencil"></i>
                        </button>
                        <button class="btn btn-danger" @click="remove(user.id)">
                            <i class="fa-solid fa-times"></i>
                        </button>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>

    <Modal v-if="showModal" @close="closeModal" title="ผู้ใช้งาน">
        <div>ชื่อ</div>
        <input type="text" class="form-control" v-model="name" />

        <div class="mt-3">username</div>
        <input type="text" class="form-control" v-model="username" />

        <div class="mt-3">password</div>
        <input type="password" class="form-control" v-model="password" />

        <div class="mt-3">ยืนยัน password</div>
        <input type="password" class="form-control" v-model="confirmPassword" />

        <div class="mt-3">ระดับสิทธิ์</div>
        <select class="form-control" v-model="level">
            <option v-for="item in listLevel" :value="item" :key="item">{{ item }}</option>
        </select>

        <button class="btn mt-3" @click="save">
            <i class="fa-solid fa-check mr-1"></i>
            บันทึก
        </button>
    </Modal>
</template>