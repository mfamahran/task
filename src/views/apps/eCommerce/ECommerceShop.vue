<!-- =========================================================================================
	File Name: ECommerceWishList.vue
	Description: eCommerce Wish List Page
	----------------------------------------------------------------------------------------
	Item Name: Vuesax Admin - VueJS Dashboard Admin Template
	Author: Pixinvent
	Author URL: http://www.themeforest.net/user/pixinvent
========================================================================================== -->

<template>
  <div id="ecommerce-shop-demo">
    <vs-row>
        <vs-col vs-w="4" class="mb-3">
            <span>Price Range</span>
            <vs-slider :min="0" :max="1000" color="warning" v-model="priceRange"/>
        </vs-col>
        <vs-col vs-w="8" class="mb-3">
            <vs-input icon-pack="feather" icon="icon-search" label-placeholder="Search" class="is-label-placeholder w-full" v-model="seachText" />
        </vs-col>
    </vs-row> 
    <vs-row>
 <vs-col vs-type="flex" vs-w="8">
    <div class="items-grid-view vx-row match-height">
        <draggable class="flex flex-wrap" group="items" :list="products">
            <div class="vx-col lg:w-1/3 md:w-1/3 sm:w-1/2 w-full mr-2" v-for="item in filterProducts" :key="item.id">
                <item-grid-view :item="item">

                    <!-- SLOT: ACTION BUTTONS -->
                    <template slot="action-buttons">
                        <div class="flex flex-wrap">

                            <!-- SECONDARY BUTTON: MOVE TO CART -->
                            <div
                                class="item-view-secondary-action-btn bg-primary p-3 flex flex-grow items-center justify-center text-white cursor-pointer"
                                @click="cartButtonClicked(item)">
                                <feather-icon icon="ShoppingBagIcon" svgClasses="h-4 w-4" />

                                <span class="text-sm font-semibold ml-2" v-if="isInCart(item)">REMOVE FROM CART</span>
                                <span class="text-sm font-semibold ml-2" v-else>ADD TO CART</span>
                            </div>
                        </div>
                    </template>
                </item-grid-view>

            </div>
            <!-- RIGHT COL -->
            </draggable>    
        </div>
  </vs-col>

   <vs-col vs-type="flex"  vs-w="4">
    <vx-card>
        <p class="text-grey mb-3">Cart</p>
        <draggable :list="cart" group="items">
            <span v-if="cart.length == 0" appear> Drag items here</span>
        <div class="items-list-view" v-for="cartItem in cart" :key="cartItem.id">
            
            <item-list-view :item="cartItem">
                
                <!-- SLOT: ACTION BUTTONS -->
                <template slot="action-buttons">
                    <div
                        class="item-view-secondary-action-btn bg-primary p-3 rounded-lg flex flex-grow items-center justify-center text-white cursor-pointer"
                        @click="cartButtonClicked(cartItem)">
                        <feather-icon icon="ShoppingBagIcon" svgClasses="h-4 w-4" />
                    </div>
                </template>
            </item-list-view>

        </div>
    </draggable>

        <vs-button class="w-full" @click="navigate()" v-if="cart.length == 0" disabled>CHECKOUT</vs-button>
        <vs-button class="w-full" @click="navigate()" v-else>CHECKOUT</vs-button>
    </vx-card>
  </vs-col>
        
    </vs-row>
  </div>
</template>

<script>
import draggable from 'vuedraggable'

const ItemGridView = () => import('./components/ItemGridView.vue')
const ItemListView = () => import('./components/ItemListView.vue')

export default {
    metaInfo: {
      title: 'Mini eCommerce',
      titleTemplate: '%s - Shop!',
      htmlAttrs: {
        lang: 'en',
        amp: true
      }
    },
    mounted (){
        this.getProducts();
    },
    data () {
        return {
            products: [],
            filteredProducts: [],
            cart: [],
            priceRange: [0, 999],
            seachText: '',
            url: 'http://localhost:8000/'
        };
    },
    components: {
        ItemGridView,
        ItemListView,
        draggable
    },
    computed: {
        isInCart() {
            return (item) => this.contains(item, this.cart);
        },
        filterProducts() {
            return this.products.filter((product) => {
                return product.name.toLowerCase().match(this.seachText.toLowerCase()) && (product.price >= this.priceRange[0] && product.price <= this.priceRange[1])
            });
        },
    },
    methods: {
        removeItemFromCart(item) {
            this.cart.splice(this.cart.indexOf(item),1);
            this.products.push(item);
        },
        cartButtonClicked(item) {
            if (this.contains(item, this.cart)) this.removeItemFromCart(item);
            else {
                this.additemInCart(item);
            }
        },
        additemInCart(item) {
            let fire = this;
            fire.cart.push(item);
        },
        getProducts(){
            let fire = this;
            axios.get('http://localhost:8000/api/products').then(function(response){
                fire.products = response.data;
                fire.products.forEach(function(elm){
                    elm.quantity = 1;
                    elm.brand = "Mini Store";
                });
            }).catch(function(error){
                console.log(error);
            });
        },
        contains(obj, list) {
            var i;
            for (i = 0; i < list.length; i++) {
                if (list[i] === obj) {
                    return true;
                }
            }

            return false;
        },
        navigate() {
            this.$router.push({name: 'eCommerceCheckout', params: {'cart': this.cart}});
        }
    }
}
</script>

<style lang="scss" scoped>
#ecommerce-shop-demo {
    .item-view-primary-action-btn {
        color: #2c2c2c !important;
        background-color: #f6f6f6;
        min-width: 50%;
    }

    .item-view-secondary-action-btn {
        min-width: 50%;
    }
}
</style>
