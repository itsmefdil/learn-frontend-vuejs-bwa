<template>
    <div>
        <!-- Breadcrumb Section Begin -->
        <div class="breacrumb-section">
            <div class="container">
                <div class="row">
                    <div class="col-lg-12">
                        <div class="breadcrumb-text product-more">
                            <router-link to="/">
                                <i class="fa fa-home"></i>
                                Home</router-link >
                            <span>Shopping Cart</span>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <!-- Breadcrumb Section Begin -->

        <!-- Shopping Cart Section Begin -->
        <section class="shopping-cart spad">
            <div class="container">
                <div class="row">
                    <div class="col-lg-8">
                        <div class="row">
                            <div class="col-lg-12">
                                <div class="cart-table">
                                    <table>
                                        <thead>
                                            <tr>
                                                <th>Image</th>
                                                <th class="p-name text-center">Product Name</th>
                                                <th>Price</th>
                                                <th>Action</th>
                                            </tr>
                                        </thead>
                                        <tbody v-if="keranjangUser.length > 0">
                                            <tr v-for="itemProduct in keranjangUser" :key="itemProduct.id">
                                                <td class="cart-pic first-row">
                                                    <img :src="itemProduct.photo"/>
                                                </td>
                                                <td class="cart-title first-row text-center">
                                                    <h5>{{ itemProduct.name }}</h5>
                                                </td>
                                                <td class="p-price first-row">Rp.{{ formatPrice(itemProduct.price) }}</td>
                                                <td class="delete-item" @click="removeItem(itemProduct.index)">
                                                    <a href="#">
                                                        <i class="material-icons">
                                                            close
                                                        </i>
                                                    </a>
                                                </td>
                                            </tr>

                                        </tbody>
                                        <tr v-else>
                                            <td colspan="4" class="text-center">
                                                <h5>Keranjang Kosong</h5>
                                            </td>
                                        </tr>
                                    </table>
                                </div>
                            </div>
                            <div class="col-lg-8 text-left">
                                <h4 class="mb-4">
                                    Informasi Pembeli:
                                </h4>
                                <div class="user-checkout ">
                                    <form>
                                        <div class="form-group">
                                            <label for="namaLengkap">Nama lengkap</label>
                                            <input
                                                type="text"
                                                class="form-control"
                                                id="namaLengkap"
                                                aria-describedby="namaHelp"
                                                placeholder="Masukan Nama"
                                                v-model="customerInfo.name"></div>
                                            <div class="form-group">
                                                <label for="namaLengkap">Email Address</label>
                                                <input
                                                    type="email"
                                                    class="form-control"
                                                    id="emailAddress"
                                                    aria-describedby="emailHelp"
                                                    placeholder="Masukan Email"
                                                    v-model="customerInfo.email"></div>
                                                <div class="form-group">
                                                    <label for="namaLengkap">No. HP</label>
                                                    <input
                                                        type="text"
                                                        class="form-control"
                                                        id="noHP"
                                                        aria-describedby="noHPHelp"
                                                        placeholder="Masukan No. HP"
                                                        v-model="customerInfo.number"></div>
                                                    <div class="form-group">
                                                        <label for="alamatLengkap">Alamat Lengkap</label>
                                                        <textarea class="form-control" id="alamatLengkap" rows="3" v-model="customerInfo.address"></textarea>
                                                    </div>
                                                </form>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="col-lg-4">
                                    <div class="row">
                                        <div class="col-lg-12">
                                            <div class="proceed-checkout">
                                                <ul>
                                                    <li class="subtotal">ID Transaction
                                                        <span>#SH12000</span>
                                                    </li>
                                                    <li class="subtotal mt-3">Subtotal
                                                        <span>Rp.{{ formatPrice(totalHarga) }}</span>
                                                    </li>
                                                    <li class="subtotal mt-3">Pajak
                                                        <span>10% ({{ formatPrice(totalHarga*10/100) }})</span>
                                                    </li>
                                                    <li class="subtotal mt-3">Total Biaya
                                                        <span>Rp.{{ formatPrice(totalHarga*10/100+totalHarga) }}</span>
                                                    </li>
                                                    <li class="subtotal mt-3">Bank Transfer
                                                        <span>Mandiri</span>
                                                    </li>
                                                    <li class="subtotal mt-3">No. Rekening
                                                        <span>2208 1996 1403</span>
                                                    </li>
                                                    <li class="subtotal mt-3">Nama Penerima
                                                        <span>Shayna</span>
                                                    </li>
                                                </ul>
                                               <a @click="checkOut" href="#" class="proceed-btn">I ALREADY PAID</a>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </section>
                    <!-- Shopping Cart Section End -->
                </div>
            </template>

            <script>
import axios from 'axios';
                export default {
                    name: 'ShoppingCartFe',
                    data() {
                        return {
                            keranjangUser: [],
                            customerInfo: {
                                name: '',
                                email: '',
                                number: '',
                                address: ''
                            }
                        };
                    },
                    methods: {
                        removeItem(index) {
                            this
                                .keranjangUser
                                .splice(index, 1);
                            const parsed = JSON.stringify(this.keranjangUser);
                            localStorage.setItem('keranjangUser', parsed);
                        },
                        formatPrice(value) {
                            let val = (value / 1)
                                .toFixed()
                                .replace('.', ',')
                            return val
                                .toString()
                                .replace(/\B(?=(\d{3})+(?!\d))/g, ".")
                        },
                        checkOut(){
                            let productIds = this.keranjangUser.map(function(product){
                                return product.id
                            });

                            let checkoutData = {
                                'name' : this.customerInfo.name,
                                'email' : this.customerInfo.email,
                                'number' : this.customerInfo.number,
                                'address' : this.customerInfo.address,
                                "transaction_total" : this.totalHarga,
                                "transaction_status": "PENDING",
                                "transaction_details": productIds
                            };
                            
                            axios
                            .post("https://bwa-be.akademi.my.id/api/checkout", checkoutData)
                            .then(() => this.$router.push('success'))

                            .catch(err => console.log(err))
                        }
                    },
                    mounted() {
                        if (localStorage.getItem('keranjangUser')) {
                            try {
                                this.keranjangUser = JSON.parse(localStorage.getItem('keranjangUser'));
                            } catch (e) {
                                localStorage.removeItem('keranjangUser');
                            }
                        }
                    },
                    computed: {
                        totalHarga() {
                            let total = 0;
                            for (let i = 0; i < this.keranjangUser.length; i++) {
                                total += this
                                    .keranjangUser[i]
                                    .price;
                            }
                            return total;
                        }
                    }
                }
            </script>

            <style scoped="scoped">
                .img-cart {
                    width: 100px;
                    height: 100px;
                }
            </style>