<template>
    <div class="branch-details">
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ name: 'branches' }">Chi nhánh</el-breadcrumb-item>
            <el-breadcrumb-item>{{ branch.name }}</el-breadcrumb-item>
        </el-breadcrumb>
        <div class="action">
            <button class="save" @click="save">Lưu</button>
            <button class="close" @click="cancel">Hủy</button>
        </div>
        <div class="main">
            <div class="info">
                <div class="code">
                    <span>Code</span>
                    <el-input v-model="branch.code" :disabled="true"></el-input>
                </div>
                <div class="name">
                    <span>Tên chi nhánh</span>
                    <el-input v-model="name"></el-input>
                </div>
                <div class="name">
                    <span>Địa chỉ</span>
                    <el-input v-model="address"></el-input>
                </div>
                <div class="name">
                    <span>Tỉnh/thành phố</span>
                    <el-input v-model="city"></el-input>
                </div>
                <div class="name">
                    <span>Quận/Huyện</span>
                    <el-input v-model="town"></el-input>
                </div>
                <div class="img">
                    <span class="title">Logo</span>
                    <el-upload class="upload-demo" list-type="picture" :auto-upload="false" :limit="1" ref="upload"
                        :on-error="onError" :on-success="onSuccess" :data="data" :file-list="fileList"
                        action="https://api.cloudinary.com/v1_1/dn1pbep3e/upload">
                        <el-button size="small" type="primary">Click to upload</el-button>
                        <div slot="tip" class="el-upload__tip">jpg/png files with a size less than 500kb</div>
                    </el-upload>
                </div>
                <div class="status">
                    <span>Trạng thái</span>
                    <div class="status-info">
                        <el-input v-model="statusDis" :disabled="true"></el-input>
                        <el-switch v-model="status"></el-switch>
                    </div>
                </div>
                <div class="created-at">
                    <span>Ngày tạo</span>
                    <el-input :disabled="true" v-model="createdAt"></el-input>
                </div>
                <div class="qrcode" flex>
                    <span>Mã Qrcode</span>
                    <div class="img" style="justify-content: space-between; align-items: center;" flex>
                        <img :src="branch.qrcode" alt="" style="width: 200px;margin-right: 20px;">
                        <!-- <button class="qr-new" @click="newQrCode">Tạo lại</button> -->
                        <i class="el-icon-download download" @click="download(branch.qrcode, branch.name)"></i>
                    </div>
                </div>
                <div class="table">
                    <span>Danh sách bàn</span>
                    <div class="tables">
                        <div style="width: 100%;display: flex;justify-content: end;">
                            <el-button class="add-table" type="success" @click="addTable">Thêm bàn</el-button>
                        </div>
                        <div class="table-item" v-for="(item, index) in tableList" :key="index">
                            <div class="qr">
                                <img v-if="item.id" :src="item.qrcode" alt="" style="width: 200px; margin-right: 20px;">
                                <span>{{ index + 1 }}.</span>
                                <el-input v-model="item.name"></el-input>
                            </div>
                            <div class="actions">
                                <i class="el-icon-download download" @click="download(item.qrcode, item.name)"></i>
                                <i class="el-icon-delete" @click="deleteTable(item)"></i>
                            </div>
                        </div>

                    </div>
                </div>
            </div>
            <div class="menu">
                <div class="header">
                    <span>MENU</span>
                </div>
                <div class="is-all"><el-checkbox v-model="isAll" style="margin-right: 40px;">Đánh dấu tất
                        cả</el-checkbox></div>

                <div class="wrapper">
                    <div class="item" v-for="item in menuList" :key="item.id">
                        <div class="product-name">
                            <el-avatar shape="square" :size="50" :src="item.img"></el-avatar>
                            <span>{{ item.productName }}</span>
                        </div>
                        <el-checkbox v-model="item.active"></el-checkbox>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from "axios";

export default {
    data() {
        return {
            branch: {},
            id: 1,
            name: "Chi nhánh 1",
            address: "",
            city: "",
            town: "",
            statusDis: "",
            status: true,
            createdAt: "",
            fileList: [],
            menuList: [],
            isAll: false,
            tableList: [
                {
                    id: 1,
                    name: "Bàn 1",
                    qrcode: "http://res.cloudinary.com/dn1pbep3e/image/upload/v1720449678/rfetoxi1bjrgp7t0sbjg.png"
                },
                {
                    id: 2,
                    name: "Bàn 1",
                    qrcode: "http://res.cloudinary.com/dn1pbep3e/image/upload/v1720449678/rfetoxi1bjrgp7t0sbjg.png"
                },
                {
                    id: 3,
                    name: "Bàn 1",
                    qrcode: "http://res.cloudinary.com/dn1pbep3e/image/upload/v1720449678/rfetoxi1bjrgp7t0sbjg.png"
                },
                {
                    id: 4,
                    name: "Bàn 1",
                    qrcode: "http://res.cloudinary.com/dn1pbep3e/image/upload/v1720449678/rfetoxi1bjrgp7t0sbjg.png"
                },
            ],
        };
    },
    computed: {
        data() {
            return {
                upload_preset: 'bhdixbmd',
                api_key: '231664969325537'
            }
        },
    },
    methods: {
        onError() {
            this.$message.error("Lỗi khi upload ảnh");
        },
        onSuccess(e) {
            this.img = e.url;
            this.updateBranch();
        },
        getBranchDetails() {
            axios.get(this.$store.state.baseUrl + "/admin/v1/branches/" + this.$route.params.id + "?userId=" + localStorage.getItem("id"), {
                headers: {
                    Authorization: "Bearer " + localStorage.getItem("user")
                }
            })
                .then(res => {
                    this.branch = res.data.data;
                    this.name = this.branch.name;
                    this.status = this.branch.status == 1;
                    this.statusDis = this.branch.statusDis;
                    this.city = this.branch.city;
                    this.town = this.branch.town;
                    this.address = this.branch.address;
                    this.createdAt = this.branch.createdAt;
                    this.img = this.branch.logo;
                    this.menuList = this.branch.menuItemRes;
                    this.tableList = this.branch.tableResponse;
                    this.fileList.push({
                        url: this.img
                    })
                })
                .catch(err => {
                    console.log(err)
                })
        },
        save() {
            if (this.name == "" || this.address == "") {
                this.$message.warning("Vui lòng nhập đầy đủ thông tin");
                return false;
            }
            if (this.$refs.upload.uploadFiles.length == 0) {
                this.$message.warning("Vui lòng chọn ảnh");
                return false;
            }
            if (this.$refs.upload.uploadFiles[0].status == "ready") this.$refs.upload.submit();
            else {
                this.updateBranch();
            }
        },
        cancel() {
            this.$router.push({ name: 'branches' });
        },
        async updateBranch() {
            if (this.checkSpace()) {
                let data = {
                    id: this.branch.id,
                    name: this.name,
                    address: this.address,
                    city: this.city,
                    town: this.town,
                    status: this.status ? 1 : 0,
                    logo: this.img,
                    createdBy: localStorage.getItem("id"),
                    menuItemRes: this.menuList,
                    tableList: this.tableList
                }
                axios.put(this.$store.state.baseUrl + "/admin/v1/branches/" + this.branch.id, data, {
                    headers: {
                        Authorization: "Bearer " + localStorage.getItem("user")
                    }
                })
                    .then(res => {
                        if (res.data.code != 2000) {
                            this.$message.error(res.data.description);
                            return false;
                        }
                        this.$message.success("Cập nhật thành công");
                        this.$router.push({ name: 'branches' });
                        return true;
                    })
                    .catch(err => {
                        console.log(err)
                    })
            }


        },
        // newQrCode(){
        //     axios.put(this.$store.state.baseUrl + "/admin/v1/branches/" + this.branch.id + "/qr", {}, {
        //         headers: {Authorization: "Bearer " + localStorage.getItem("user") }
        //     }).then(res => {
        //         if (res.data.code != 2000) {
        //             this.$message.error(res.data.description);
        //             return false;
        //         }
        //         this.$message.success("Tạo mã Qr mới thành công");
        //         this.branch.qrcode = res.data.data.qrCode;
        //         return true;
        //     }).catch(err => {
        //         console.log(err)
        //     })
        // }
        download(url, name) {
            axios.get(url, { responseType: 'blob' })
                .then(response => {
                    const blob = new Blob([response.data], { type: 'application/png' })
                    const link = document.createElement('a')
                    link.href = URL.createObjectURL(blob)
                    link.download = name + ".png"
                    link.click()
                    URL.revokeObjectURL(link.href)
                }).catch(console.error)
        },
        checkSpace() {
            if (this.name == "" || this.address == "") {
                this.$message.warning("Vui lòng nhập đầy đủ thông tin");
                return false;
            }

            if (this.tableList.some(item => !item.name)) {
                this.$message.warning("Vui lòng nhập tên bàn");
                return false;
            }

            return true;

        },
        addTable() {
            if (this.tableList.length == 0) {
                this.tableList.push({
                    name: "",
                    qrcode: ""
                });
                return;
            }
            this.tableList.unshift({
                name: "",
                qrcode: ""
            });
        },
        deleteTable(table) {
            this.tableList = this.tableList.filter(item => item != table);
        }
    },
    mounted() {
        this.getBranchDetails();
    },
    watch: {
        isAll: function (val) {
            this.menuList.forEach(item => {
                item.active = val;
            });
        }
    }
};
</script>

<style scoped>
>>>.el-checkbox__inner {
    transform: scale(1.2);
}

.el-input>>>input {
    height: 30px;
    font-size: 15px;
    font-family: "sarabun";
}

.branch-details {
    width: 100%;
    height: 100vh;
    display: flex;
    flex-direction: column;
}

.el-breadcrumb {
    height: 30px;
    padding-left: 40px;
    display: flex;
    align-items: center;
    font-size: 14px;
}

.action {
    width: 100%;
    display: flex;
    margin-top: 10px;
    height: 35px;
}

.close,
.save {
    height: 30px;
    width: 100px;
    background-color: white;
    border: 1px solid #797979;
    border-radius: 5px;
    font-size: 14px;
    margin-left: 40px;
}

.close:hover,
.save:hover {
    background-color: #f5f5f5;
}

.main {
    display: flex;
    width: 100%;
    height: calc(100% - 75px);
}

.info {
    width: 55%;
    border-right: 1px solid #949494;
    display: flex;
    flex-direction: column;
    align-items: start;
    padding: 30px 40px 10px 40px;
    height: calc(100% - 40px);
    overflow-y: auto;
}

.qrcode span {
    height: 60px;
}

.name,
.code,
.description,
.img,
.category-filter,
.status,
.created-at,
.status,
.qrcode,
.table {
    display: flex;
    justify-content: space-between;
    width: 100%;
    margin-bottom: 25px;
}

.name span,
.qrcode span,
.code span,
.description span,
.img .title,
.status span,
.created-at span,
.category-filter .title,
.table span {
    text-align: left;
    font-size: 15px;
    color: #575757;
    display: flex;
    align-items: center;
}

.description span {
    padding-bottom: 120px;
}

.name .el-input,
.qrcode .img,
.code .el-input,
.description .el-textarea,
.img .upload-demo,
>>>.category-filter .el-dropdown,
.created-at .el-input,
.status .status-info,
.tables {
    width: 68%;
    border-radius: 5px;
}

.status-info {
    display: flex;
    width: 68%;
    align-items: center;
}

.status-info .el-input {
    width: 30%;
    margin-right: 50px;
}

.el-textarea>>>textarea {
    font-family: 'sarabun';
    font-size: 15px;
    height: 150px;
}

.upload-demo {
    text-align: left;
}

.menu {
    height: 100%;
    width: 40%;
}

.wrapper {
    height: calc(100% - 132px);
    background-color: white;
    display: flex;
    flex-direction: column;
    align-items: start;
    padding: 20px 30px 20px 30px;
    margin: 15px 40px 10px 40px;
    gap: 20px;
    overflow-y: auto;
    /* border-top: 1px solid #949494; */
    /* border-bottom: 1px solid #949494; */
    box-shadow: 0px 5px 5px -3px rgba(0, 0, 0, 0.2), 0px 8px 10px 1px rgba(0, 0, 0, 0.14), 0px 3px 14px 2px rgba(0, 0, 0, 0.12);
    border-radius: 20px;
}

.item {
    width: 100%;
    display: flex;
    flex-direction: row;
    justify-content: space-between;
}

.product-name {
    display: flex;
    flex-direction: row;
    align-items: center;
}

.product-name .el-avatar {
    margin-right: 10px;
}

.is-all {
    margin: auto;
    width: 100%;
    text-align: right;
    margin-top: 15px;
    font-size: 18px;
}

.is-all>>>.el-checkbox__label {
    font-size: 15px;
    font-weight: 400;
}

.qr-new {
    height: 35px;
    width: 100px;
    background: white;
    border: 1px solid;
    border-radius: 5px;
    margin-top: 10px;
}

.qr-new:hover {
    background-color: #f5f5f5;
}

.table {
    align-items: start;
    border-top: 1px solid #c4c4c4;
    padding-top: 20px;
}

.tables {
    text-align: left;
}

.table-item {
    display: flex;
    align-items: center;
    margin-bottom: 20px;
    justify-content: space-between;
}

.table-item .el-input {
    width: 80%;
    margin-left: 10px;
    min-width: 110px;
    max-width: 190px;
}

.download {
    font-size: 20px;
    margin: 10px;
    color: #000000;
}

.download:hover {
    cursor: pointer;
    color: #0041f5;
}

.actions {
    display: flex;
}

.el-icon-delete {
    font-size: 20px;
    margin: 10px;
    color: #000000;
}

.el-icon-delete:hover {
    color: #ff0000;
    cursor: pointer;
}

.qr {
    display: flex;
    align-items: center;
}

.add-table {
    width: 110px;
    height: 35px;
    margin-left: 12px;
    border-radius: 5px;
    border: none;
    font-size: 15px;
    display: flex;
    align-items: center;
    margin-bottom: 20px;
}
</style>