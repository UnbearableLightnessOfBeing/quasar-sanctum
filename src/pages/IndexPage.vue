<script setup lang="ts">
import { ref } from 'vue';
import axios from 'axios';

axios.get('http://localhost/sanctum/csrf-cookie').then((response) => {
    console.log(response);
});

type User = {
    name: string;
    email: string;
};

const user = ref<null | User>(null);

const getUser = () => {
    axios
        .get('http://localhost/api/user')
        .then((response) => {
            console.log(response);

            if (response.data) {
                user.value = response.data as User;
            }
        })
        .catch((error) => {
            console.log(error);
            user.value = null;
        });
};

getUser();

const pending = ref(false);

const logout = () => {
    pending.value = true;
    axios
        .post('http://localhost/api/logout')
        .then((res) => {
            console.log(res);
            pending.value = false;
        })
        .catch((error) => {
            console.log(error);
            pending.value = false;
        });

    getUser();
};
</script>

<template>
    <q-page class="q-gutter-lg" padding>
        <q-card class="row items-start justify-evenly">
            <q-card-section v-if="!user">
                <q-btn label="Login" to="/login"></q-btn>
            </q-card-section>
            <q-card-section v-if="!user">
                <q-btn label="Register" to="/register"></q-btn>
            </q-card-section>
            <q-card-section v-if="user">
                <q-btn
                    :loading="pending"
                    label="Logout"
                    @click="logout"
                ></q-btn>
            </q-card-section>
        </q-card>
        <q-card class="q-pa-sm row justify-center">
            <div v-if="!user" class="text-negative">you are not logged in</div>
            <div v-else class="text-positive">you are logged in</div>
        </q-card>
    </q-page>
</template>
