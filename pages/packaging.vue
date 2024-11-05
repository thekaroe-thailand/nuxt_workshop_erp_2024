<script setup>

import Swal from 'sweetalert2';
import axios from 'axios';
import config from '@/config';

definePageMeta({
    layout: 'admin'
});

const showModalAddPackaging = ref(false);
const id = ref('');
const name = ref('');
const remark = ref('');
const packagings = ref([]);

onMounted(async () => {
    await fetchData();
});

const closeModalAddPackaging = () => {
    showModalAddPackaging.value = false;
}

const save = async () => {
    try {
        const payload = {
            name: name.value,
            remark: remark.value
        }

        if (id.value == '') {
            await axios.post(`${config.apiServer}/api/packaging/create`, payload);
        } else {
            await axios.put(`${config.apiServer}/api/packaging/update/${id.value}`, payload);
            id.value = '';
        }

        closeModalAddPackaging();
        fetchData();
    } catch (error) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: error.message
        });
    }
}

const fetchData = async () => {
    try {
        const response = await axios.get(`${config.apiServer}/api/packaging/list`);
        packagings.value = response.data.results;
    } catch (error) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: error.message
        });
    }
}

const remove = async (id) => {
    try {
        const button = await Swal.fire({
            icon: 'warning',
            title: 'ยืนยันการลบ',
            text: 'คุณต้องการลบบรรจุภัณฑ์นี้หรือไม่?',
            showCancelButton: true,
            showCancelButton: true
        });

        if (button.isConfirmed) {
            await axios.delete(`${config.apiServer}/api/packaging/remove/${id}`);
            fetchData();
        }
    } catch (error) {
        Swal.fire({
            icon: 'error',
            title: 'error',
            text: error.message
        });
    }
}

const edit = (packaging) => {
    id.value = packaging.id;
    name.value = packaging.name;
    remark.value = packaging.remark;

    showModalAddPackaging.value = true;
}
</script>

<template>
    <div class="title">บรรจุภัณฑ์</div>
    <div class="p-4">
        <button class="btn" @click="showModalAddPackaging = true">
            <i class="fa-solid fa-plus"></i>
            เพิ่มบรรจุภัณฑ์
        </button>

        <table class="table mt-3">
            <thead>
                <tr>
                    <th width="200px" class="text-left">ชื่อบรรจุภัณฑ์</th>
                    <th class="text-left">หมายเหตุ</th>
                    <th width="110px"></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="item in packagings" :key="item.id">
                    <td>{{ item.name }}</td>
                    <td>{{ item.remark }}</td>
                    <td>
                        <button class="btn btn-primary me-1" @click="edit(item)">
                            <i class="fa fa-pencil"></i>
                        </button>
                        <button class="btn btn-danger" @click="remove(item.id)">
                            <i class="fa fa-times"></i>
                        </button>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>

    <Modal v-if="showModalAddPackaging" @close="closeModalAddPackaging" title="เพิ่มบรรจุภัณฑ์">
        <div>ชื่อบรรจุภัณฑ์</div>
        <input type="text" v-model="name" class="form-control" />

        <div class="mt-3">หมายเหตุ</div>
        <input type="text" v-model="remark" class="form-control" />

        <button class="btn btn-primary mt-3" @click="save">
            <i class="fa-solid fa-check me-2"></i>
            บันทึก
        </button>
    </Modal>
</template>