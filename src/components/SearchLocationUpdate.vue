<template>
    
    <div>
        <div>
        <h1>Search Location Code</h1>
    </div>
        <input type="text" v-model="valueE" style="padding: 5px 5px; border-radius: 10px;">
        <button class="btn" style="border: 1px solid greenyellow; margin: 0 10px;" @click="findOneData(valueE, page)">Search</button>
        <button @click="downloadData" class="btn" style="border: 1px solid greenyellow; margin: 0 10px;">Download Excel</button>
    </div>

    <div v-if="dataProduct.length > 0" style="margin: 20px 0;">
        <table class="table">
        <thead>
          <tr>
            <th class="title">Title</th>
            <th class="title">Area</th>
            <th class="title">Line</th>
            <th class="title">Shelf</th>
            <th class="title">Location</th>
            <!-- <th class="title">History</th> -->
          </tr>
        </thead>
        <tbody>
          <tr v-for="(itemData, indexData) in dataProduct" :key="indexData">
            <td>{{ itemData.title }}</td>
            <td>{{ itemData.area }}</td>
            <td>{{ itemData.line }}</td>
            <td>{{ itemData.shelf }}</td>
            <td>{{ itemData.code }}</td>
            <!-- <td v-if="itemData?.history?.length > 0">
                <div v-for="(item, index) in itemData?.history" :key="index">
                    <p > 
                    {{ item.area }} => {{ item.line }} => {{ item.shelf }}

                    => {{ item.code_location_addr }}
                </p>
                <span v-if="index != itemData?.history.length - 1">⏫</span>
                </div>
            </td> -->
          </tr>
        </tbody>
      </table>
        
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