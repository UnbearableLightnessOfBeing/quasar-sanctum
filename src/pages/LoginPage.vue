<script setup lang="ts">
import { reactive, ref } from 'vue';
import axios from 'axios';
import { Notify } from 'quasar';

const form = reactive({
    email: '',
    password: '',
});

const errors = reactive({
    email: '',
    password: '',
});

const pending = ref(false);

const onSubmit = () => {
    pending.value = true;
    axios
        .post('http://localhost/api/login', form)
        .then((res) => {
            console.log(res);
            pending.value = false;

            Notify.create({
                message: 'You are logged in',
                color: 'positive',
                icon: 'mdi-check',
            });
        })
        .catch((error) => {
            console.log(error);
            const resErrors = error.response.data.errors;

            if (resErrors.email) {
                errors.email = resErrors.email;
            }
            if (resErrors.password) {
                errors.email = resErrors.password;
            }
            pending.value = false;
        });
};
</script>

<template>
    <div>
        <div class="q-ma-lg">
            <q-breadcrumbs class="text-orange" active-color="secondary">
                <template v-slot:separator>
                    <q-icon size="1.2em" name="arrow_forward" color="purple" />
                </template>
                <q-breadcrumbs-el icon="mdi-home" label="Home" to="/" />
                <q-breadcrumbs-el label="Login" icon="mdi-login" />
            </q-breadcrumbs>
        </div>
        <q-card class="q-ma-lg">
            <q-form class="q-gutter-sm q-pa-lg" @submit.prevent="onSubmit">
                <div>
                    <q-input type="email" label="email" v-model="form.email" />
                    <div class="text-negative">{{ errors.email }}</div>
                </div>
                <div>
                    <q-input
                        type="password"
                        label="password"
                        v-model="form.password"
                    />
                    <div class="text-negative">{{ errors.password }}</div>
                </div>
                <div class="row justify-end">
                    <q-btn
                        :loading="pending"
                        class="q-mt-lg"
                        label="login"
                        type="submit"
                    ></q-btn>
                </div>
            </q-form>
        </q-card>
    </div>
</template>

<style scoped></style>
