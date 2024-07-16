<template>
    <div v-loading="loading" class="authen"></div>
</template>

<script>
import axios from 'axios';
import { jwtDecode } from 'jwt-decode';

export default {
    data() {
        return {
            loading: true
        }
    },
    mounted() {
        localStorage.removeItem('user');
        localStorage.removeItem('branchId');
        localStorage.removeItem('tableNumber');
        localStorage.removeItem('id');
        console.log("mounted");
        this.genToken();
        this.loading = false;
    },
    methods: {
        genToken() {
            const code = this.$route.query.code;
            const tableId = this.$route.query.tableId ? this.$route.query.tableId : 0;
            console.log(tableId);

            axios.get(this.$store.state.baseUrl + '/auth/v1/guest-token?' +
                'code=' + code +
                '&tableId=' + tableId
            ).then(response => {
                const jwt = JSON.stringify(response.data.data.jwt).replace(/\"/g, "");
                const id = jwtDecode(jwt).id;
                const branchId = jwtDecode(jwt).branchId;
                const tableId = jwtDecode(jwt).tableId ? jwtDecode(jwt).tableId : 0;
                localStorage.setItem('user', jwt);
                localStorage.setItem('id', id);
                localStorage.setItem('branchId', branchId);
                localStorage.setItem('tableId', tableId);
                this.$router.push('/');
            }).catch(error => {
                console.log(error);
                this.$router.push('/error');
            })
        }
    }
}
</script>

<style scoped>
div {
    width: 100vw;
    height: 100vh;
    background: rgba(27, 27, 27, 0.493);
}
</style>