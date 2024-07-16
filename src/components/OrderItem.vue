<template>
    <div class="order-item">
        <div class="start">
            <div class="wrapper">
                <span class="code"><span style="color: #888; font-weight: 300;">Code: </span>{{ order.code }}</span>
                <span class="datetime">{{ order.orderAt }}</span>
            </div>
            <div class="wrapper" style="align-items: end;">
                <span class="total">{{ order.total }}</span>
                <span class="status" :style="statusColor(order.status)">{{ order.status }}</span>
            </div>
        </div>
        <div class="main">
            <div class="item" v-for="item in order.orderItemList" v::key="item.id">
                <img :src="item.productImg" alt="">
                <div class="info">
                    <span class="name">{{ item.productName }}</span>
                    <span class="size" v-if="item.hasSize">size {{ item.sizeSelected }}</span>
                    <div class="price">
                        <span class="price-display">{{ item.productPrice }}</span>
                        <span class="quantity">x{{ item.quantity }}</span>
                    </div>
                    <span class="note">{{ item.note }}</span>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'OrderItem',
    props: ['order'],
    data() {
        return {
        }
    },
    methods: {
        statusColor(status) {
            switch (status) {
                case "Chờ xác nhận":
                    return { color: "#FFA500" };
                case "Đang pha chế":
                    return { color: "#3278CA" };
                case "Đã hủy":
                    return { color: "#CF2127" };
                case "Hoàn thành":
                    return { color: "#0D9F1C" };
                default:
                    return {};
            }
        },
    }
}

</script>