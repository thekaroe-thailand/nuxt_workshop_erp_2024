<script setup>
import config from '@/config';
import axios from 'axios';
import Swal from 'sweetalert2';

definePageMeta({
    layout: 'admin',
});

const name = ref('');
const username = ref('');
const password = ref('');
const confirmPassword = ref('');
const level = ref('admin');

onMounted(() => {
    fetchData();
});

const fetchData = async () => {
    try {
        const token = localStorage.getItem(config.token);
        const headers = {
            'Authorization': `Bearer ${token}`,
        };

        const response = await axios.get(config.apiServer + '/api/user/info', { headers });

        name.value = response.data.result.name;
        username.value = response.data.result.username;
        level.value = response.data.result.level;
    } catch (e) {
        Swal.fire({
            icon: 'error',
            title: 'Error',
            text: e.message,
        })
    }
}

const save = async () => {
    try {
        const token = localStorage.getItem(config.token);
        const headers = {
            'Authorization': `Bearer ${token}`,
        };

        if (password.value !== confirmPassword.value) {
            Swal.fire({
                icon: 'warning',
                title: 'รหัสผ่านไม่ตรงกัน',
                text: 'กรุณาตรวจสอบรหัสผ่านอีกครั้ง',
                showConfirmButton: true
            });

            return;
        }

        const payload = {
            name: name.value,
            username: username.value,
            password: password.value,
            level: level.value,
        };

        await axios.put(config.apiServer + '/api/user/update', payload, { headers });

        Swal.fire({
            icon: 'success',
            title: 'บันทึกข้อมูล',
            text: 'บันทึกข้อมูลสำเร็จ',
            timer: 1000,
        })
    } catch (e) {
        Swal.fire({
            icon: 'error',
            title: 'Error',
            text: e.message,
        })
    }
}

</script>

<template>
    <div class="title">ข้อมูลส่วนตัว</div>
    <div class="p-4">
        <div class="">
            <div class="">
                <div class="form-group">
                    <div>ชื่อ</div>
                    <input type="text" class="form-control" v-model="name" />

                    <div class="mt-3">username</div>
                    <input type="text" class="form-control" v-model="username" />

                    <div class="mt-3">รหัสผ่าน <span class="text-red">(ถ้าต้องการเปลี่ยน ให้กรอกข้อมูล)</span></div>
                    <input type="password" class="form-control" v-model="password" />

                    <div class="mt-3">ยืนยันรหัสผ่าน <span class="text-red">(ถ้าต้องการเปลี่ยน ให้กรอกข้อมูล)</span>
                    </div>
                    <input type="password" class="form-control" v-model="confirmPassword" />

                    <div class="mt-3">สิทธิ์การเข้าสู่ระบบ</div>
                    <input type="radio" id="admin" v-model="level" value="admin" class="me-1" />
                    <label for="admin" class="me-2">Admin</label>
                    <input type="radio" id="user" v-model="level" value="user" class="me-1" />
                    <label for="user">User</label>

                    <div class="mt-3">
                        <button class="btn btn-primary" @click="save">
                            <i class="fa fa-check mr-1"></i>
                            บันทึก
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>