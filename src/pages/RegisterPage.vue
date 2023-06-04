<script setup lang="ts">
import axios from 'axios';
import { ref, reactive } from 'vue';
import { Notify } from 'quasar';

const form = reactive({
    name: '',
    email: '',
    password: '',
});

const resetForm = (): void => {
    form.name = '';
    form.email = '';
    form.password = '';
};

const errors = reactive({
    name: '',
    email: '',
    password: '',
});

const resetErrors = () => {
    errors.name = '';
    errors.email = '';
    errors.password = '';
};

const pending = ref(false);

const onSubmit = () => {
    resetErrors();
    pending.value = true;
    axios
        .post('http://localhost/api/register', form)
        .then((response) => {
            console.log(response.data);
            resetForm();
            pending.value = false;
            Notify.create({
                message: 'User has been created',
                color: 'positive',
                position: 'bottom-right',
                icon: 'mdi-check',
            });
        })
        .catch((error) => {
            console.log(error);
            const resErrors = error.response.data.errors;

            if (resErrors.name) {
                errors.name = error.response.data.errors.name[0];
            }
            if (resErrors.email) {
                errors.email = error.response.data.errors.email[0];
            }
            if (resErrors.password) {
                errors.password = error.response.data.errors.password[0];
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
                <q-breadcrumbs-el label="Register" icon="mdi-pen" />
            </q-breadcrumbs>
        </div>
        <q-card class="q-ma-lg">
            <q-form class="q-gutter-sm q-pa-lg" @submit.prevent="onSubmit">
                <div>
                    <q-input type="text" label="name" v-model="form.name" />
                    <div class="text-negative">{{ errors.name }}</div>
                </div>
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
                        label="register"
                        type="submit"
                    ></q-btn>
                </div>
            </q-form>
        </q-card>
    </div>
</template>

<style scoped></style>
