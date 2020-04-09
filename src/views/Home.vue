<template>
  <div class="home">
    <h1 style="color:blue;">{{storeName }}</h1>
       
      <p><button v-on:click="addProduct()"> add a new product</button></p>
      Name:<input type="text" v-model="newProductName">
      <p>Price:<input type="text" v-model="newProductPrice"></p>
      <P>Description:<input type="text" v-model="newProductDescription"></p>
      <P>Image_url:<input type="text" v-model="newProductImage_url"></P>
      <hr>
      <div v-bind:key="product.id" v-for="product in products">
      <p>id: {{product.id}}</p>
      <p>name: {{product.name}}</p>
      <p>price: {{product.price}}</p>
      <p>tax: {{product.tax}}</p>
      <p>total: {{product.total}}</p>
      
      <button v-on:click="showInfo(product)">Show more info</button>
      <dif v-if="currentProduct === product">
      <p>description: {{product.description}}</p>
      <p>image_url: {{product.image_url}}</p>
      <img v-bind:src="product.image_url"style="width:250px;height:200px;">  
      </dif>
      <p>Name: <input type="text" v-model="product.name"></p>
      <p>description: <input type="text" v-model="product.description"></p>
      <p>price: <input type="text" v-model="product.price"></p>
      <p>image: <input type="text" v-model="product.image_url"></p>
      <p><button v-on:click="updateProduct(product)">Update this product</button></p> 
      <button v-on:click="deleteProduct(product)">Delete Product</button>    
      <hr>

    </div>
  </div>
</template>

<style>
</style>

<script>
import axios from "axios";
   
export default {
  data: function() {
    return {
      storeName: "ArianyMart",
      products: [],
      newProductName: "",
      newProductPrice: "",
      newProductDescription: "",
      newProductImage_url: "",
      currentProduct: {}
    };
  },
  created: function() {
    console.log('in the created')
    axios.get("/api/products").then(response => {
    console.log(response)
      this.products = response.data;
    })
  },

 
  methods: {
    addProduct: function(){
      console.log(this.newProductName)
       //get data
       var params = {
         name: this.newProductName,
         price: this.newProductPrice,
         description: this.newProductDescription,
         image_url: this.newProductImage_url
       }
       // send it to rails
       axios.post("/api/products", params).then(response => {
        console.log(response.data);
        this.products.push(response.data)
        this.newProductName = " " ;
        this.newProductPrice = " " ;
        this.newProductDescription = " " ;
        this.newProductImage_url = " "; 
       })
    },
    showInfo: function(theProduct){ 
      console.log('show info')
      this.currentProduct = theProduct;
    },

    updateProduct: function(theProduct){
      console.log('update product')
      var params = {
      name: theProduct.title,
      description: theProduct.description,
      price: theProduct.price,
      image: theProduct.image
      }
       axios.patch(`/api/products/${theProduct.id}`, params).then(response => {
        console.log(response.data);
        theProduct = response.data;
        })
    },
    deleteProduct: function(theProduct){
      console.log('delete product')
      //make the web request to rails
      axios.delete('/api/products/' + theProduct.id).then(response =>{
      console.log(response.data)
      //delete in the web page
      //find the index
      var index = this.products.indexOf(theProduct)
      // then delete
      this.products.splice(index, 1)
      })
    }
  }
};
</script>