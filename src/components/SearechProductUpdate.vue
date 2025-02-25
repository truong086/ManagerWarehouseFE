<template>
    
    <div>
        <div>
          <div style="margin-bottom: 20px;">
            <h1 style="font-weight: bold;">Search Product Code</h1>
          </div>
          <div>
            <input type="text" v-model="searchName" style="padding: 5px 5px; border-radius: 10px; border: 1px dashed greenyellow;">
            <button class="btn" style="border: 1px solid greenyellow; margin: 0 10px;" @click="findOneData(currentDataSuppliers, page)">Search</button>
            <button class="btn" style="border: 1px solid greenyellow;" @click="dowloadData">
              Download Excel
            </button>
          </div>
          <div>
        </div>

      <div style="margin: 30px 0; display: flex; width: 100%; justify-content: center;">
        <h3 style="margin: 0 20px;">Supplier: </h3>
        <select v-model="currentDataSuppliers" @change="searchDataByProduct">
        <option v-for="(item, index) in suppliersData" :key="index" :value="item.id">
          {{ item.title }}
        </option>
      </select>
      </div>

      
      </div>
      </div>

    <div v-if="dataProduct.length > 0" style="width: 100%; margin: 20px 0; cursor: pointer;">
        <table class="table">
        <thead>
          <tr>
            <th class="title">Title</th>
            <th class="title">Warehouse ID</th>
            <th class="title">Area</th>
            <th class="title">Line</th>
            <th class="title">Shelf</th>
            <th class="title">Quantity</th>
            <th class="title">Location</th>
            <th class="title" v-if="dataProduct?.history?.length > 0">History</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(itemProduct, indexProduct) in dataProduct" :key="indexProduct" @click="showData(itemProduct.id)">
            <td>{{ itemProduct.title }}</td>
            <td>{{ itemProduct.nameSupplier }}</td>
            <td>{{ itemProduct.dataLocations[0].area }}</td>
            <td>{{ itemProduct.dataLocations[0].line }}</td>
            <td>{{ itemProduct.dataLocations[0].shelf }}</td>
            <td>{{ itemProduct.dataLocations[0].quantity }}</td>
            <td>{{ itemProduct.dataLocations[0].code }}</td>
            <!-- <td v-if="itemProduct?.history?.length > 0">
                <div v-for="(item, index) in itemProduct?.history" :key="index">
                <p>
                    {{ item.code_location_addr }}
                </p>
                <span v-if="index != itemProduct?.history.length - 1">⏫</span>
                </div>
            </td> -->
          </tr>
        </tbody>
      </table>
        
      <PagesTotal :page="page" :totalPage="totalPage" :valueE="currentDataSuppliers" @pageChange="findOneData" @pageSizeChange="changeReload"></PagesTotal>
      
    </div>

    <div>
        <h1>{{ dataNull }}</h1>
      </div>
    
    
    <div v-if="isLoading" class="loading-overlay">
      <div class="spinner"></div>
      <p>Đang tải...</p>
    </div>

    <div v-if="frameVisibleNew" class="frame-popup">
        <div
          class="frame-content"
          :style="{ maxWidth: '800' + 'px', justifyContent: 'flex-start' }"
        >
        <button class="btn" style="border: 1px solid black;" @click="closeFaram">
          close
        </button>

        <button class="btn" @click="dowloadExcelOneData(Dataframe.title)" style="border: 1px solid greenyellow;">
          Download Excel
        </button>
          <div
            class="frame-item"
            
            style="margin: 10px 50px"
          >
            <h3>{{ Dataframe?.title }}</h3>
            
            <div class="frame-info">
              <div class="info-line">
                <span class="info-title">Quantity:</span> {{ Dataframe?.quantity }}
                <span class="info-title">Warehouse ID:</span> {{ Dataframe?.supplier }}
              </div>
              <!-- <button @click="closeFrame" v-if="item.id_plan == 0" class="close-btn">Swap</button> -->
            </div>
            
            <div v-if="Dataframe?.history?.length > 0">
              <h2>History Product</h2>
              <table class="table">
        <thead>
          <tr>
            <th class="title">Area</th>
            <th class="title">Line</th>
            <th class="title">Shelf</th>
            <th class="title">Code</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(itemProduct, indexProduct) in Dataframe?.history" :key="indexProduct">
            <td>{{ itemProduct.area}}</td>
            <td>{{ itemProduct.line }}</td>
            <td>{{ itemProduct.shelf }}</td>
            <td>{{ itemProduct.code_location_addr }}</td>
            
          </tr>
        </tbody>
      </table>
            </div>
            

            <div v-if="Dataframe.inOutByProducts.length > 0">
              <h2>Deliverynote And Import</h2>
              <table class="table">
        <thead>
          <tr>
            <th class="title">status</th>
            <th class="title">location</th>
            <th class="title">updateat</th>
            <th class="title">quantity</th>
            <th class="title" v-if="Dataframe?.history?.length > 0">History</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(itemProduct, indexProduct) in Dataframe.inOutByProducts" :key="indexProduct">
            <td>{{ itemProduct.status == 0 ? "Deliverynote" : "Import" }}</td>
            <td>{{ itemProduct.location }}</td>
            <td>{{ itemProduct.updateat }}</td>
            <td>{{ itemProduct.quantity }}</td>
            
          </tr>
        </tbody>
      </table>
            </div>

      
          </div>
        </div>
      </div>
</template>

<script setup>
import axios from 'axios';
import {ref, getCurrentInstance, watch, onMounted} from 'vue';
  import {useToast} from 'vue-toastification'
  import PagesTotal from './PageList/PagesTotal.vue'

  const {proxy} = getCurrentInstance()
  const hostname = proxy?.hostname
  const Toast = useToast()
  const isLoading = ref(false)
  const searchName = ref('')
  const suppliersData = ref([])
  const page = ref(1)
  const totalPage = ref(0)
  const pageSize = ref(20)

  const frameVisibleNew = ref(false)
  const Dataframe = ref([])
  const dataProduct = ref([])
  const currentDataSuppliers = ref(null)

  const dataNull = ref("")
  onMounted(() => {
    findAllSupplier()
  })

  const closeFaram = () => {
    frameVisibleNew.value = !frameVisibleNew.value
  }
  
  watch(page.value, (newPage) => {
    findOneData(currentDataSuppliers.value, newPage)
  })
  const changeReload = (event) => {
    pageSize.value = event
    findOneData(currentDataSuppliers.value, page.value)
  }

  const dowloadExcelOneData = async (title) => {
    isLoading.value = true;
    document.body.classList.add("loading"); // Add Lớp "loading"
    document.body.style.overflow = "hidden";
    const res = await axios.post(hostname + `/api/Product/FindAllDownLoadExcelByCodeProduct?code=${title}`, {}, {
      headers: {
                    'Accept': 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
                    'Content-Type': 'application/json',
                },
                responseType: 'blob' // Cực kỳ quan trọng! Không có sẽ bị lỗi file
    })

    const url = window.URL.createObjectURL(new Blob([res.data]));
        const a = document.createElement('a');
        a.href = url;
        a.download = getCurrentTimestamp(); // Đặt tên file khi tải xuống
        document.body.appendChild(a);
        a.click();
        a.remove();
        window.URL.revokeObjectURL(url);

    isLoading.value = false;
    document.body.classList.remove("loading");
    document.body.style.overflow = "auto";
  }
  const showData = async (id) => {
    isLoading.value = true;
    document.body.classList.add("loading"); // Add Lớp "loading"
    document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + `/api/Product/findOneByOutAndIn?id=${id}`)

    if(res.data.success){
      frameVisibleNew.value = true
      Dataframe.value = res.data.content
      console.log(res)
    }

    console.log(res)
    isLoading.value = false;
    document.body.classList.remove("loading");
    document.body.style.overflow = "auto";
  }

  const dowloadData = async () => {

    let data = []
    console.log(dataProduct.value)
    if(dataProduct.value.length <= 0)
        return

        isLoading.value = true;
    document.body.classList.add("loading"); // Add Lớp "loading"
    document.body.style.overflow = "hidden";

    dataProduct.value.forEach(element => {
      data.push(element.title)
    })

    if(currentDataSuppliers.value != null){
      const res = await axios.post(hostname + `/api/Product/FindAllDownLoadExcelBySupplier?supplier=${currentDataSuppliers.value}`, {}, {
      headers: {
                    'Accept': 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
                    'Content-Type': 'application/json',
                },
                responseType: 'blob' // Cực kỳ quan trọng! Không có sẽ bị lỗi file
    })

    const url = window.URL.createObjectURL(new Blob([res.data]));
        const a = document.createElement('a');
        a.href = url;
        a.download = getCurrentTimestamp(); // Đặt tên file khi tải xuống
        document.body.appendChild(a);
        a.click();
        a.remove();
        window.URL.revokeObjectURL(url);
    }else{
      const res = await axios.post(hostname + '/api/Product/FindAllDownLoadExcelByCodeProductList', data, {
      headers: {
                    'Accept': 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
                    'Content-Type': 'application/json',
                },
                responseType: 'blob' // Cực kỳ quan trọng! Không có sẽ bị lỗi file
    })

    const url = window.URL.createObjectURL(new Blob([res.data]));
        const a = document.createElement('a');
        a.href = url;
        a.download = getCurrentTimestamp(); // Đặt tên file khi tải xuống
        document.body.appendChild(a);
        a.click();
        a.remove();
        window.URL.revokeObjectURL(url);
    }
    

    isLoading.value = false;
    document.body.classList.remove("loading");
    document.body.style.overflow = "auto";
  }

  const getCurrentTimestamp = () => {
    const now = new Date();
    const year = now.getFullYear();
    const month = String(now.getMonth() + 1).padStart(2, "0");
    const day = String(now.getDate()).padStart(2, "0");
    const hours = String(now.getHours()).padStart(2, "0");
    const minutes = String(now.getMinutes()).padStart(2, "0");
    const seconds = String(now.getSeconds()).padStart(2, "0");

    return `ProductCode_${year}${month}${day}_${hours}${minutes}${seconds}.xlsx`;
};

  const findAllSupplier = async () => {

    isLoading.value = true;
    document.body.classList.add("loading"); // Add Lớp "loading"
    document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + '/api/Supplier/FindAll')

    if(res.data.success){
      suppliersData.value = res.data.content
    }

    isLoading.value = false;
    document.body.classList.remove("loading");
    document.body.style.overflow = "auto";
  }
  const searchDataByProduct = () => {
    searchName.value = ''
    findOneData(currentDataSuppliers.value, page.value)
  }

  const findOneData = async(search, pageData) => {

    if(!searchName.value.trim() && search == null){
      dataNull.value = "No Data"
      return
    }
        
  isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
  if(!searchName.value.trim()){
    const res = await axios.get(hostname + `/api/Product/findBySuppliers?id=${search}&page=${pageData}&pageSize=${pageSize.value}`)
    searchName.value = ''
    if(res.data.success){
      if(res.data.content.data.length > 0){
        dataProduct.value = []
        dataProduct.value = res.data.content.data
        page.value = res.data.content.page
        totalPage.value = res.data.content.totalPages
        if(page.value > totalPage.value)
          page.value = 1
        dataNull.value = ""
        Toast.success("Success")
      }else{
        dataProduct.value = []
        dataNull.value = "No Data"
      }
      
    }else{
        dataProduct.value = []
        dataNull.value = "No Data"
    }
  }else{
    const res = await axios.get(hostname + `/api/Product/FindOne?name=${searchName.value}&page=${pageData}&pageSize=${pageSize.value}`)
    currentDataSuppliers.value = null
    if(res.data.success){
      if(res.data.content.data.length > 0){
        dataProduct.value = []
        dataProduct.value = res.data.content.data
        page.value = res.data.content.page
        totalPage.value = res.data.content.totalPages
        dataNull.value = ""
        Toast.success("Success")
      }else{
        dataProduct.value = []
        dataNull.value = "No Data"
      }
      
    }else{
        dataProduct.value = []
        dataNull.value = "No Data"
    }
    console.log(res)
  }
    
    
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