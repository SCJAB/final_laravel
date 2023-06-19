<template>
    <div class="mx-auto max-w-5xl py-10">
        <h3 class="text-2xl">Add User</h3>
        <div class="py-3">
            <button @click="back()" class="shadow-md px-4 py-1 hover:bg-violet-50">Back</button>
        </div>
        <div>
            <form class="space-y-3" @submit.prevent="saveUser()">
                <div>
                    <FormLabel label="Name" class="text-violet-700"/>
                    <FormTextField class="shadow-md border-opacity-0" name="name" placeholder="Name" v-model="state.user.name" />
                    <div v-if="state.errors && state.errors.data && state.errors.data.errors && state.errors.data.errors.name" class="text-red-500 text-sm">
                        {{ state.errors.data.errors.name[0] }}
                    </div>
                </div>
                <div>
                    <FormLabel label="Course" class="text-violet-700"/>
                    <FormTextField class="shadow-md border-opacity-0" name="course" placeholder="Course" v-model="state.user.course" />
                    <div v-if="state.errors && state.errors.data && state.errors.data.errors && state.errors.data.errors.course" class="text-red-500 text-sm">
                        {{ state.errors.data.errors.course[0] }}
                    </div>
                </div>
                <div>
                    <FormLabel label="Email" class="text-violet-700"/>
                    <FormTextField class="shadow-md border-opacity-0" name="email" placeholder="Email" v-model="state.user.email" />
                    <div v-if="state.errors && state.errors.data && state.errors.data.errors && state.errors.data.errors.email" class="text-red-500 text-sm">
                        {{ state.errors.data.errors.email[0] }}
                    </div>
                </div>
                <div>
                    <FormLabel label="Password" class="text-violet-700"/>
                    <div class="relative">
                        <FormTextField class="shadow-md border-opacity-0" :type="showPassword ? 'text' : 'password'" name="password" placeholder="password" v-model="state.user.password"/>
                        <button type="button" class="absolute top-1/2 right-3 transform -translate-y-1/2 text-violet-500" @click="showPassword = !showPassword">
                            <svg v-if="showPassword" viewBox="0 0 24 24" fill="none" stroke="currentColor" class="w-14 h-14">
                            <circle cx="20" cy="12" r="3" />
                        </svg>
                            <svg v-else viewBox="0 0 24 24" fill="currentColor" stroke="currentColor" class="w-14 h-14">
                            <circle cx="20" cy="12" r="3" />
                        </svg>
                        </button>
                    </div>
                    <div v-if="state.errors && state.errors.data && state.errors.data.errors && state.errors.data.errors.password" class="text-red-500 text-sm">
                        {{ state.errors.data.errors.password[0] }}
                    </div>
                </div>
                <div>
                    <FormLabel label="Status" class="text-violet-700"/>
                    <br />
                    <FormCheckbox :value="state.user.is_active ? true : false"
                        @click="state.user.is_active = !state.user.is_active" />
                </div>
                <div>
                    <FormButton type="submit" buttonStyle="primary" class="w-full bg-opacity-10 bg-violet-700 text-violet-700 shadow-xl hover:bg-violet-200">Save</FormButton>
                </div>
            </form>
        </div>
    </div>
</template>
  
<script setup>
const showPassword = ref(false);

const state = reactive({
    user: {
        name: null,
        course: null,
        password: null,
        email: null,
        is_active: false,
    },
    errors: null,
})

function back() {
    navigateTo('/')
}

async function saveUser() {
    const payload = {
        name: state.user.name,
        email: state.user.email,
        password: state.user.password,
        course: state.user.course,
        is_active: state.user.is_active,
    }
    await $fetch(`http://127.0.0.1:8000/api/users`, {
        method: 'POST',
        body: payload
    }).then((result) => {
        if (result) {
            alert('Successfully saved.')
        }
    }).catch((errors) => {
        state.errors = errors
    })
}
</script>