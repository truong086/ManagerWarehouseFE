<template>
    <div>
        <div>
            <h1>Search Product Code</h1>
        </div>
        <input type="text" v-model="searchName" style="padding: 5px 5px; border-radius: 10px;">
        <button class="btn" style="border: 1px solid greenyellow; margin: 0 10px;" @click="findOneData">Search</button>
    </div>

    <div v-if="dataProduct.title != undefined" style="width: 100%; margin: 20px 0; cursor: pointer;">
        <table class="table">
        <thead>
          <tr>
            <th class="title">Title</th>
            <th class="title">Area</th>
            <th class="title">Line</th>
            <th class="title">Shelf</th>
            <th class="title">Location</th>
            <th class="title" v-if="dataProduct?.history?.length > 0">History</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>{{ dataProduct.title }}</td>
            <td>{{ dataProduct.dataLocations[0].area }}</td>
            <td>{{ dataProduct.dataLocations[0].line }}</td>
            <td>{{ dataProduct.dataLocations[0].shelf }}</td>
            <td>{{ dataProduct.dataLocations[0].code }}</td>
            <td v-if="dataProduct?.history?.length > 0">
                <div v-for="(item, index) in dataProduct?.history" :key="index">
                    <!-- <p > 
                    {{ item.area }} {{ item.line }} {{ item.shelf }}

                     {{ item.code_location_addr }}
                </p> -->
                <p>
                    {{ item.code_location_addr }}
                </p>
                <!-- <span v-if="index != itemData?.history.length - 1">⏫</span> -->
                </div>
            </td>
          </tr>
        </tbody>
      </table>
        
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

<style>
.table-page {
  padding: 20px;
  font-family: 'Arial', sans-serif;
  background-color: #f9f9f9;
  color: #333;
  display: flex;
  flex-direction: column;
  align-items: center;
}

h1 {
  text-align: center;
  font-size: 2rem;
  margin-bottom: 30px;
  color: #4CAF50;
}

.table-container {
  width: 100%;
  margin-bottom: 30px;
}

.table-title {
  text-align: center;
  font-size: 1.5rem;
  font-weight: bold;
  margin-bottom: 15px;
  color: #4CAF50;
}

.table {
  width: 100%;
  border-collapse: collapse;
  background-color: #fff;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  border-radius: 10px;
}

.title {
  text-align: center;
  font-size: 1.1rem;
  color: #555;
  background-color: #f2f2f2;
  padding: 10px;
  font-weight: bold;
  border-radius: 10px;
}

.table th,
.table td {
  padding: 12px;
  text-align: center;
  border: 1px solid #ddd;
  font-size: 1rem;
}

.table td {
  background-color: #fff;
  color: #666;
}

.table tr:nth-child(even) td {
  background-color: #f9f9f9;
}

.table tr:hover td {
  background-color: #e1e1e1;
  cursor: pointer;
}
</style>