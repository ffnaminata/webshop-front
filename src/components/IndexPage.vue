<template>
    <div class="IndexPage">
        <br>
        <div class="row">
            <!-- Banner -->
            <div class="jumbotron custom-banner">
                <h1 class="display-4">VueJS Shopping App</h1>
            </div>

            <!-- Product list --> 
            <div class="col-md-9">
                <div class="row gx-4 gy-4 row-cols-3">
                    <div class="col" v-for="(product, id) in products" :key="id">
                        <div class="p-3 border bg-light">
                            <h5>{{ product.name }}</h5>
                            <p>{{ product.desc }}</p>
                            <p>{{ product.price }} </p>
                            <button type="button" class="btn btn-success btn-sm" v-on:click="add_to_cart(product._id)"> Ajouter au panier </button>
                        </div> 
                    </div>
                </div> 
            </div>

            <!-- Cart -->
            <div class="col-6 col-md-3">
                <div class="col" >
                    <div class="p-3 border bg-success">
                        <h5>Cart </h5>
                    </div>
                </div>
                <div class="row gx-4 row-cols-1">
                    <div class="col" v-for="(value, id) in list_cart" :key="id">
                        <div class="p-3 border bg-light">
                            <h5>{{value.product.name}}</h5>
                            <h6>Quantity : {{value.quantity}}</h6>
                            <button type="button" class="btn btn-success btn-sm" v-on:click="remove_from_cart(value.product._id)"> Supprimer </button>
                        </div> 
                    </div>
                    <!-- show total --> 
                    <div class="col" >
                        <div class="p-3 border bg-success">
                            <h5>Total : {{total_price}} </h5> 
                            <h6>Nombre de produits : {{nbr_product}} </h6>
                        </div> 
                    </div>
                </div> 
            </div>
        </div> 
    </div>
</template>

<script>
import axios from 'axios';

export default {
    name: 'IndexPage', props: {
        msg: String 
    },
    data() {
        return {
            products: [],
            list_cart: [],
            total_price: 0,
            nbr_product: 0,
            error: null,
        };
    },
    created() {
        this.fetchProducts();
        this.fetchCart();
    },
    methods: {
        async fetchProducts() {
            try {
                const response = await axios.get('http://localhost:5000/api/products/');
                this.products = response.data.result;
                console.log(this.products);
            } catch (error) {
                this.error = 'Failed to fetch products.';
            }
        },

        async fetchCart() {
            try {
                const response = await axios.get('http://localhost:5000/api/carts/');
                this.list_cart = response.data.cart.products;
                await this.update_total();
            } catch (error) {
                this.error = 'Failed to fetch cart.';
            }
        },

        async add_to_cart(id) {
            try {
                const response = await axios.post(`http://localhost:5000/api/carts/add/${id}`);
                this.list_cart = response.data.cart.products;
                await this.update_total();
            } catch (error) {
                this.error = 'Failed to add to cart.';
            }
        },

        async update_total() {
            try {
                const response = await axios.get('http://localhost:5000/api/carts/summary');
                this.total_price = response.data.total_price;
                this.nbr_product = response.data.nbr_product;
            } catch (error) {
                this.error = 'Failed to update cart total.';
            }
        },

        async remove_from_cart(id) {
            try {
                const response = await axios.post(`http://localhost:5000/api/carts/remove/${id}`);
                this.list_cart = response.data.cart.products;
                await this.update_total();
            } catch (error) {
                this.error = 'Failed to remove from cart.';
            }
        },
    },
};
</script>

<style scoped>
.custom-banner {
    background-color: #f2f2f2;
    padding: 20px; 
    margin: 20px;
    border-radius: 10px;
}  
</style>