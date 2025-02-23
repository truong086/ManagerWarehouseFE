<template>
    
    <div>
        <div>
        <h1 style="font-weight: bold; margin-bottom: 20px">Search Location Code</h1>
    </div>
        <input type="text" v-model="valueE" style="padding: 5px 5px; border-radius: 10px; border: 1px dashed greenyellow;">
        <button class="btn" style="border: 1px solid greenyellow; margin: 0 10px;" @click="findOneData(valueE, page)">Search</button>
        <button @click="downloadData" class="btn" style="border: 1px solid greenyellow; margin: 0 10px;">Download Excel</button>
        
        <div style="margin: 30px 0; display: flex; width: 100%; justify-content: center;">
          <div style="display: flex;">
            <h3 style="margin: 0 10px;">Area: </h3>
          <select style="margin: 0 10px; width: 80px;" v-model="currentArea" @change="searchLine">
          <option v-for="(item, index) in DataArea" :key="index" :value="item">{{ item }}</option>
        </select>
          </div>

        <div style="display: flex; margin: 0 10px;">
          <h3 style="margin: 0 10px;">Line: </h3>
          <select v-model="currentLine" @change="searchShelf" style="width: 80px;">
          <option v-for="(item, index) in DataLine" :key="index" :value="item">{{ item }}</option>
        </select>
        </div>

        <div style="display: flex;">
          <h3 style="margin: 0 10px;">Shelf: </h3>
          <select style="width: 80px;" v-model="currentShelf" @change="searchLocation">
          <option v-for="(item, index) in DataShelf" :key="index" :value="item.shelf">{{ item.shelf }}</option>
        </select>
        </div>

        <div style="display: flex; margin: 0 10px; width: 80px;">
          <h3 style="margin: 0 10px;">Location: </h3>
          <select v-model="currentLocation" @change="ClickData">
          <option v-for="(item, index) in dataUpdateLocation" :key="index" :value="item">{{ item }}</option>
        </select>
        </div>
        </div>
    </div>

    <div v-if="dataProduct.length > 0" style="margin: 20px 0;">
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
            <!-- <th class="title">History</th> -->
          </tr>
        </thead>
        <tbody>
          <tr v-for="(itemData, indexData) in dataProduct" :key="indexData"  @click="showData(itemData.id)">
            <td>{{ itemData.title }}</td>
            <td>{{ itemData.supplier }}</td>
            <td>{{ itemData.area }}</td>
            <td>{{ itemData.line }}</td>
            <td>{{ itemData.shelf }}</td>
            <td>{{ itemData.quantity }}</td>
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

    <div v-if="frameVisibleNew" class="frame-popup">
        <div
          class="frame-content"
          :style="{ maxWidth: '800' + 'px', justifyContent: 'flex-start' }"
        >
        <button class="btn" style="border: 1px solid black;" @click="closeFaram">
          close
        </button>

        <button class="btn" @click="dowloadExcelOneData(Dataframe.title)" style="border: 1px solid greenyellow;">
          DowLoad Excel
        </button>

        <div v-if="Dataframe?.history?.length > 0">
                <div v-for="(itemData, indexData) in Dataframe?.history" :key="indexData">
                    <!-- <p > 
                    {{ item.area }} {{ item.line }} {{ item.shelf }}

                     {{ item.code_location_addr }}
                </p> -->
                <p>
                    {{ itemData.code_location_addr }}
                </p>
                <span v-if="indexData != Dataframe?.history.length - 1">⏫</span>
                </div>
            </div>
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

  const page = ref(1)
  const totalPage = ref(0)
  const pageSize = ref(20)
  const valueE = ref('')

  const currentArea = ref(null)
  const currentLine = ref(null)
  const currentShelf = ref(null)
  const currentLocation = ref(null)

  const DataArea = ref([])
  const DataLine = ref([])
  const DataShelf = ref([])
  const DataLocation = ref([])
  const dataProduct = ref([])
  const dataUpdateLocation = ref([])
  const frameVisibleNew = ref(false)
  const Dataframe = ref({})

  onMounted(() => {
    dataArea()
  })

  const closeFaram = () => {
    frameVisibleNew.value = !frameVisibleNew.value
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
        a.download = 'LocationCode' + new Date().toLocaleTimeString() + '.xlsx'; // Đặt tên file khi tải xuống
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

    isLoading.value = false;
    document.body.classList.remove("loading");
    document.body.style.overflow = "auto";
  }
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
        a.download = 'LocationCode' + new Date().toLocaleTimeString() + '.xlsx'; // Đặt tên file khi tải xuống
        document.body.appendChild(a);
        a.click();
        a.remove();
        window.URL.revokeObjectURL(url);

        isLoading.value = false;
    document.body.classList.remove("loading");
    document.body.style.overflow = "auto";
  }

  const searchLine = () => {
    valueE.value = ''
    DataLine.value = []
    DataShelf.value = []
    DataLocation.value = []
    currentLine.value = null
    currentLocation.value = null
    currentShelf.value = null
    dataLine()

    valueE.value = currentArea.value
    console.log(valueE.value)

  }
  
  const searchShelf = () => {
    valueE.value = ''
    DataShelf.value = []
    DataLocation.value = []
    currentLocation.value = null
    currentShelf.value = null
    dataShelf()
    const chuyendoi = parseInt(currentLine.value)
    if(chuyendoi <= 9 && !/^0\d+/.test(currentLine.value) && !/^0\d+/.test(currentShelf.value))
      valueE.value = currentArea.value + '0' + currentLine.value
    else 
      valueE.value = currentArea.value +  '' + currentLine.value
    console.log(valueE.value)
  }

  const searchLocation = () => {
    valueE.value = ''
    DataLocation.value = []
    currentLocation.value = null
    dataLocation()

    const chuyendoi = parseInt(currentLine.value)
    const chuyendoiShelf = parseInt(currentShelf.value)
    if(chuyendoi <= 9 && chuyendoiShelf <= 9 && !/^0\d+/.test(currentLine.value) && !/^0\d+/.test(currentShelf.value))
        valueE.value = currentArea.value + '0' + currentLine.value + '0' + currentShelf.value
    
    else if(chuyendoi <= 9 && chuyendoiShelf > 9 && !/^0\d+/.test(currentLine.value) && !/^0\d+/.test(currentShelf.value))
        valueE.value = currentArea.value + '0' + currentLine.value + '' + currentShelf.value
    else if(chuyendoi > 9 && chuyendoiShelf <= 9 && !/^0\d+/.test(currentLine.value) && !/^0\d+/.test(currentShelf.value))
        valueE.value = currentArea.value + '' + currentLine.value + '0' + currentShelf.value
    else valueE.value = currentArea.value + '' + currentLine.value + '' + currentShelf.value
  }

  const ClickData = () => {
    valueE.value = ''
    dataLine()
    const chuyendoi = parseInt(currentLine.value)
    const chuyendoiShelf = parseInt(currentShelf.value)
    if(chuyendoi <= 9 && chuyendoiShelf <= 9 && !/^0\d+/.test(currentLine.value) && !/^0\d+/.test(currentShelf.value))
        valueE.value = currentArea.value + '0' + currentLine.value + '0' + currentShelf.value + '' + currentLocation.value
    
    else if(chuyendoi <= 9 && chuyendoiShelf > 9 && !/^0\d+/.test(currentLine.value) && !/^0\d+/.test(currentShelf.value))
        valueE.value = currentArea.value + '0' + currentLine.value + '' + currentShelf.value + '' + currentLocation.value
    else if(chuyendoi > 9 && chuyendoiShelf <= 9 && !/^0\d+/.test(currentLine.value) && !/^0\d+/.test(currentShelf.value))
        valueE.value = currentArea.value + '' + currentLine.value + '0' + currentShelf.value + '' + currentLocation.value
    else valueE.value = currentArea.value + '' + currentLine.value + '' + currentShelf.value + '' + currentLocation.value
  }
  const dataArea = async () => {
    isLoading.value = true;
    document.body.classList.add("loading"); // Add Lớp "loading"
    document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + `/api/location_addr/FindAllData`)
    if(res.data.success){
      DataArea.value = res.data.content.sort((a, b) => {
        const letterA = a.charAt(0) // Lấy ký tự đầu tiên
        const letterb = b.charAt(0) // Lấy ký tự đầu tiên
        
        const numA = parseInt(a.slice(1))
        const numB = parseInt(b.slice(1))

        // Sắp xếp theo chữ cái đầu tiên, nếu giống thì sắp xếp theo số
        if(letterA == letterb){
          return numA - numB
        }

        return letterA.localeCompare(letterb)
      })
    }

    isLoading.value = false;
    document.body.classList.remove("loading");
    document.body.style.overflow = "auto";
  }

  const dataLine = async () => {
    isLoading.value = true;
    document.body.classList.add("loading"); // Add Lớp "loading"
    document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + `/api/location_addr/FindAllDataLine?area=${currentArea.value}`)
    if(res.data.success){
      DataLine.value = res.data.content
      DataLine.value = [...DataLine.value].sort((a, b) => parseInt(a) - parseInt(b))
    }

    isLoading.value = false;
    document.body.classList.remove("loading");
    document.body.style.overflow = "auto";
  }

  const dataShelf = async () => {
    isLoading.value = true;
    document.body.classList.add("loading"); // Add Lớp "loading"
    document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + `/api/location_addr/FindAllDataShelfOne?line=${currentLine.value}&area=${currentArea.value}`)
    if(res.data.success){
      DataShelf.value = res.data.content
      DataShelf.value = [...DataShelf.value].sort((a, b) => parseInt(a.shelf) - parseInt(b.shelf))
    }

    isLoading.value = false;
    document.body.classList.remove("loading");
    document.body.style.overflow = "auto";
  }

  const dataLocation = async () => {
    isLoading.value = true;
    document.body.classList.add("loading"); // Add Lớp "loading"
    document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + `/api/location_addr/FindAllDataLocation?line=${currentLine.value}&area=${currentArea.value}&shelf=${currentShelf.value}`)
    if(res.data.success){
      DataLocation.value = res.data.content
      DataLocation.value.location = [...DataLocation.value.location].sort((a, b) => {
        const numA = parseInt(a.slice(-2))
        const numB = parseInt(b.slice(-2))

        return numA - numB
      }) 
      DataLocation.value.location.map((item) => {
        dataUpdateLocation.value.push(item.slice(-2)) // Cắt lấy 2 số cuối
      })
    }

    console.log(res)

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