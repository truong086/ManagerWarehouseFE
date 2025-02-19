<template>
    <div>
        <input type="text" v-model="searchName" style="padding: 5px 5px; border-radius: 10px;">
        <button class="btn" style="border: 1px solid greenyellow; margin: 0 10px;" @click="findOneData">Search</button>
    </div>

    <div v-if="dataProduct.length > 0" style="width: 100%; cursor: pointer; display: flex; flex-wrap: wrap;">
        <div style="border: 1px solid black; width: 200px; border-radius: 10px; margin: 0 auto;" v-for="(itemData, indexData) in dataProduct" :key="indexData">
            <div>
                <h2>{{ itemData.title }}</h2>
            </div>
            <div v-if="itemData?.dataLocations?.length > 0">
                <p> 
                    {{ itemData.dataLocations[0].area }} => {{ itemData.dataLocations[0].line }} => {{ itemData.dataLocations[0].shelf }}

                    => {{ itemData.dataLocations[0].code }}
                </p>
            </div>
            <div v-if="itemData?.history?.length > 0">
                <h3>Histoty: </h3>
                <div v-for="(item, index) in itemData?.history" :key="index">
                    <p > 
                    {{ item.area }} => {{ item.line }} => {{ item.shelf }}

                    => {{ item.code_location_addr }}
                </p>
                <span v-if="index != itemData?.history.length - 1">⏫</span>
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

  const dataProduct = ref([])
  const findOneData = async() => {
    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + `/api/Product/findOneCode?name=${searchName.value}`)
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