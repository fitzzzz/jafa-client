<template>
    <b-container>
        <centered-layout>
            <h2>Access The Shops</h2>
            <v-select placeholder="Search Shop"
                      :options=shops v-model=selectedShop class="form-control"></v-select>
            <div v-if="selectedShop" class="mx-3">
                <b-nav justified tabs>
                    <b-nav-item @click="shopNavigation(false)" :active="!catalog">Practical Informations</b-nav-item>
                    <b-nav-item @click="shopNavigation(true)" :active="catalog">Catalog</b-nav-item>
                </b-nav>
                <br/>
                <ShopInfo v-if="selectedShop && !catalog" :shop="selectedShop.value"/>
                <div v-if="selectedShop && catalog">
                    <FoodItems :food-list="shopFoodList"></FoodItems>
                    <b-pagination-nav base-url="#/shops/" :number-of-pages="nbPages" v-model="currentPage"/>
                </div>
            </div>
        </centered-layout>
    </b-container>
</template>

<script>
    import CenteredLayout from "../layouts/CenteredLayout";
    import ShopInfo from "../sub/ShopInfo";
    import FoodItems from "../sub/FoodItems";


    export default {
        name: 'ShopList',
        components: {FoodItems, ShopInfo, CenteredLayout},
        data() {
            return {
                shops: [],
                selectedShop: null,
                currentPage: 1,
                nbPages: 1,
                shopFoodList: [],
                catalog: false
            }
        },
        watch: {
            selectedShop: function () {
                this.getFoodListOfShop().then(foodList => this.shopFoodList = foodList);
            },
            currentPage: function () {
                this.getFoodListOfShop().then(foodList => this.shopFoodList = foodList);
            }
        },
        methods: {
            async getFoodListOfShop() {
                let url = new URL(this.JAFA_SERVER + "foods");
                let params = {
                    order: "desc",
                    criteria: "price",
                    shop: this.selectedShop.value._id,
                    page: this.currentPage,
                };
                url.search = new URLSearchParams(params);
                return await fetch(url)
                    .catch((error) => console.error(error))
                    .then((response) => response.json())
                    .then((data) => {
                        this.nbPages = Math.ceil(data.count / 15);
                        return data.data.map((elem) => {
                            return {id: elem.id, name: elem.name, value: elem.price}
                        });
                    });
            },
            shopNavigation(catalog) {
                this.catalog = catalog;
            }
        },
        mounted() {
            fetch(this.JAFA_SERVER + "stores")
                .catch((error) => console.error(error))
                .then((response) => response.json())
                .then((data) => {
                    this.shops = data.data.map((elem) => {
                        return {label: elem.name, value: elem}
                    });
                });
        }
    }
</script>

