<template>
    
    <div>
        <div>
        <h1>Search Location Code</h1>
    </div>
        <input type="text" v-model="valueE" style="padding: 5px 5px; border-radius: 10px;">
        <button class="btn" style="border: 1px solid greenyellow; margin: 0 10px;" @click="findOneData(valueE, page)">Search</button>
        <button @click="downloadData" class="btn" style="border: 1px solid greenyellow; margin: 0 10px;">Download Excel</button>
    </div>

    <div v-if="dataProduct.length > 0" style="width: 100%; cursor: pointer; display: flex; flex-wrap: wrap;">
        <div style="border: 1px solid black; width: 200px; border-radius: 10px; margin: 0 auto;" v-for="(itemData, indexData) in dataProduct" :key="indexData">
            <div>
                <h2>{{ itemData.title }}</h2>
            </div>
            <div>
                <p> 
                    {{ itemData.area }} => {{ itemData.line }} => {{ itemData.shelf }}

                    => {{ itemData.code }}
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

    <PagesTotal :page="page" :totalPage="totalPage" :valueE="valueE" @pageChange="findOneData" @pageSizeChange="changeReload"></PagesTotal>
    
    <div v-if="isLoading" class="loading-overlay">
      <div class="spinner"></div>
      <p>Đang tải...</p>
    </div>
</template>

<script setup>
import axios from 'axios';
import {ref, getCurrentInstance, watch} from 'vue';
  import {useToast} from 'vue-toastification'
  import PagesTotal from './PageList/PagesTotal.vue'

  const {proxy} = getCurrentInstance()
  const hostname = proxy?.hostname
  const Toast = useToast()
  const isLoading = ref(false)

  const page = ref(1)
  const totalPage = ref(0)
  const pageSize = ref(20)
  const valueE = ref('')

  const downloadData = async () =>{
    if(!valueE.value.trim()){
        return
    }

    isLoading.value = true;
    document.body.classList.add("loading"); // Add Lớp "loading"
    document.body.style.overflow = "hidden";

    const res = await axios.get(hostname + `/api/location_addr/dowloadexcel?code=${valueE.value}`, {
      headers: {
                    'Accept': 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
                    'Content-Type': 'application/json',
                },
                responseType: 'blob' // Cực kỳ quan trọng! Không có sẽ bị lỗi file
    });

    console.log(res)
    const url = window.URL.createObjectURL(new Blob([res.data]));
        const a = document.createElement('a');
        a.href = url;
        a.download = 'DataExcel.xlsx'; // Đặt tên file khi tải xuống
        document.body.appendChild(a);
        a.click();
        a.remove();
        window.URL.revokeObjectURL(url);

        isLoading.value = false;
    document.body.classList.remove("loading");
    document.body.style.overflow = "auto";
  }
  const changeReload = (event) => {
    pageSize.value = event
    findOneData(valueE.value, page.value)
  }

  watch(page.value, (newPage) => {
    findOneData(valueE.value, newPage)
  })

  const dataProduct = ref([])
  const findOneData = async(search, pageData) => {
    console.log(search)
    isLoading.value = true;
    document.body.classList.add("loading"); // Add Lớp "loading"
    document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + `/api/location_addr/SearchData?name=${search}&page=${pageData}&pageSize=${pageSize.value}`)
    if(res.data.success){
        dataProduct.value = res.data.content.data
        page.value = res.data.content.page
        totalPage.value = res.data.content.totalPages
        Toast.success("Success")
    }else{
        dataProduct.value = []
    }
    
    console.log(res)
    isLoading.value = false;
    document.body.classList.remove("loading");
    document.body.style.overflow = "auto";
  }
</script>