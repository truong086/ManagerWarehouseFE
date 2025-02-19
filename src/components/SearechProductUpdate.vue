<template>
    <div>
        <div>
            <h1>Search Product Code</h1>
        </div>
        <input type="text" v-model="searchName" style="padding: 5px 5px; border-radius: 10px;">
        <button class="btn" style="border: 1px solid greenyellow; margin: 0 10px;" @click="findOneData">Search</button>
    </div>

    <div v-if="dataProduct.title != undefined" style="width: 100%; cursor: pointer;">
        <div style="border: 1px solid black; width: 200px; border-radius: 10px; margin: 0 auto;">
            <div>
                <h2>{{ dataProduct.title }}</h2>
            </div>
            <div v-if="dataProduct?.dataLocations?.length > 0">
                <p> 
                    {{ dataProduct.dataLocations[0].area }} => {{ dataProduct.dataLocations[0].line }} => {{ dataProduct.dataLocations[0].shelf }}

                    => {{ dataProduct.dataLocations[0].code }}
                </p>
            </div>
            <div v-if="dataProduct?.history?.length > 0">
                <h3>Histoty: </h3>
                <div v-for="(item, index) in dataProduct?.history" :key="index">
                    <p > 
                    {{ item.area }} => {{ item.line }} => {{ item.shelf }}

                    => {{ item.code_location_addr }}
                </p>
                <span v-if="index != dataProduct?.history.length - 1">⏫</span>
                </div>
                
            </div>
        </div>
        
    </div>
    
    <div v-if="isLoading" class="loading-overlay">
      <div class="spinner"></div>
      <p>Đang tải...</p>
    </div>
</template>

<script setup>
import axios from 'axios';
import {ref, getCurrentInstance} from 'vue';
  import {useToast} from 'vue-toastification'

  const {proxy} = getCurrentInstance()
  const hostname = proxy?.hostname
  const Toast = useToast()
  const isLoading = ref(false)
  const searchName = ref('')

  const dataProduct = ref({})
  const findOneData = async() => {
    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + `/api/Product/FindOne?name=${searchName.value}`)
    if(res.data.success){
        dataProduct.value = res.data.content
        Toast.success("Success")
    }else{
        dataProduct.value = {}
    }
    console.log(res)
    
    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
  }
</script>