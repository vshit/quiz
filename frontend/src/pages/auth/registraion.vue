<template>
    <div class="page">
        <form @submit.prevent="Submit">
            <h1>Registration</h1>
            <input v-model="formData.email" type="text" placeholder="Email">
            <input v-model="formData.password" :type="showPassword ? 'text' : 'password' " placeholder="Password">
            <div class="show_password">
                <input id="showPassword" v-model="showPassword" name="Show password" type="checkbox">    
                <label for="showPassword">Show password</label>
            </div>
            <button type="submit">Submit</button>
            <p 
            class="signin">
            Already have an account?
            <router-link to="/login">Sign In</router-link>
            </p>
        </form>
    </div>
</template>

<script lang="ts">
import { defineComponent, reactive, ref } from 'vue';
import { useRouter } from 'vue-router';
import { useNotification } from "@kyvg/vue3-notification";

export default defineComponent({
    setup() {
        const formData = reactive({
            email: '',
            password: ''
        })

        const showPassword = ref(false)
        const router = useRouter()
        const {notify} = useNotification()

        async function Submit() {
            if (!formData.email.includes("@")){
                return alert('Email must include @')
            }

            if(formData.password.length < 4 && formData.password.length > 16) {
                return alert('Password doesn`t pass')
            }

            try {
                const res = await fetch('http://localhost:8080/auth/registration', {
                    method: 'POST',
                    headers: {
                        'content-type': 'application/json'
                    },
                    body: JSON.stringify({
                        login: formData.email,
                        password: formData.password
                    })
                    
                })
                const data = await res.json()
                const access_token = data.access_token
                if (access_token) {
                    localStorage.setItem('token', access_token)
                    router.push('/test')
                }
                if(data.message.includes('exists')) {
                    notify({
                        title: 'Account already exists',
                        text: data.message,
                        type: 'warn'
                    })
                    router.push('/login')
                }
            } catch (error) {
                alert('Error message')
            }
        }
        return {
            formData,
            Submit,
            showPassword
        }
    }
})

</script>

<style scoped>

.page {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    background: linear-gradient(to top right, #ace2ad, #17ada0);
}
h1 {
    text-align: center;
    color: #17ada0;
    font-size: 36px;
    font-weight: 600;
}
form {
    border-radius: 8px;
    background-color: #fff;
    padding: 20px;
    width: 450px;
    display: flex;
    flex-direction: column;
    gap: 25px;
}
input {
    padding: 8px 15px;
    font-size: 16px;
    outline: none;
}
input:active,input:focus {
    border: 2px solid #17ada0;
}
.show_password {
    display: flex;
    gap: 5px;
    align-items: center;
}
button {
    padding: 8px 15px;
    width: 200px;
    border: none;
    margin: 0 auto;
    background-color: #17ada0;
    color: #fff;
    text-transform: uppercase;
    font-size: 16px;
}
.signin {
    text-align: center;

}
p a {
    color: #17ada0;
    text-decoration: none;
}
</style>