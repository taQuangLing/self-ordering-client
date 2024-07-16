<template>
    <div class="my-order">
        <span class="header">ĐƠN ĐẶT CỦA TÔI</span>
        <div class="main">
            <OrderItem v-for="order in orders" :key="order.id" :order="order" class="order-item" />
        </div>
    </div>
</template>

<script>
import OrderItem from '@/components/OrderItem.vue';
import axios from 'axios';

export default {
    name: 'App',
    components: {
        OrderItem
    },
    data() {
        return {
            orders: [],
        }
    },
    methods: {
        async getOrders() {
            await axios.get(this.$store.state.baseUrl + "/guest/v1/orders/" + localStorage.getItem("id"),
                { headers: { Authorization: `Bearer ${localStorage.getItem('user')}` } })
                .then(response => {
                    this.orders = response.data.data;
                })
                .catch(error => {
                    console.log(error);
                });

        }
    },
    beforeMount() {
        this.getOrders();
        this.interval = setInterval(this.getOrders, 5000);
    },
    beforeDestroy() {
        clearInterval(this.interval);
    }
}
</script>

<style scoped>
.my-order {
    display: flex;
    flex-direction: column;
    justify-content: start;
    height: 100%;
}

.header {
    color: #333;
    font-size: 18px;
    font-weight: 400;
    margin: 10px 0 10px 0;
    text-align: left;
}

.order-item {
    margin-bottom: 25px;
}

.main {
    flex-grow: 1;
    overflow-y: auto;
    scrollbar-width: thin;
    scrollbar-color: transparent transparent;
}

.main::-webkit-scrollbar {
    width: 6px;
}

.main::-webkit-scrollbar-thumb {
    background-color: transparent;
}

.main::-webkit-scrollbar-track {
    background-color: transparent;
}

.main>>>.order-item {
    .wrapper {
        display: flex;
        flex-direction: column;
        align-items: start;
    }
    .wrapper .total {
        font-size: 17px;
        font-weight: 500;
        color: #451717;
    }
    .wrapper .code {
        font-size: 16px;
    }


    .start {
        display: flex;
        justify-content: space-between;
        margin-bottom: 5px;
    }

    .main {
        padding: 0px 5px 0px 10px;
        background-color: #ececec;
        border-radius: 7px;
    }

    .datetime {
        color: #656565;
        font-size: 14px;
        font-weight: 200;
    }

    .status {
        font-size: 15px;
        font-weight: 400;
    }

    .item {
        display: flex;
        padding: 10px 0 10px;
    }

    img {
        width: 65px;
        height: 65px;
        border-radius: 5px;
    }

    .info {
        display: flex;
        flex-direction: column;
        align-items: start;
        margin-left: 10px;
    }

    .name {
        font-size: 16px;
        font-weight: bold;
        color: #333;
        margin-bottom: 2px;
        text-align: left;
    }

    .size {
        color: #888;
        font-size: 13px;
    }

    .note {
        font-size: 12px;
        color: #5a5a5a;
        margin-bottom: 2px;
    }

    .price {
        margin-top: 3px;
    }

    .price-display {
        font-size: 16px;
        font-weight: bold;
        color: #B2470B;
        margin-right: 5px;
    }

    .quantity {
        font-size: 15px;
        font-weight: 500;
        color: #363636;
    }

    
}
</style>