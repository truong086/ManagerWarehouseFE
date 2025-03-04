<template>
    <div class="container mt-4">
      <div class="header">
        <label for="floorSelect" class="form-label"> 選擇樓層</label>

    <div class="dropdown-container">
    <!-- Nhấn vào sẽ mở dropdown -->
    <div class="dropdown-select" @click="toggleDropdowns">
      <span v-if="dataAreaSearch.length === 0">Chọn dữ liệu</span>
      <span v-else>{{ dataAreaSearch.map(item => item).join(", ") }}</span>
      <span class="arrow">▼</span>

      <button
                  class="row-label"
                  @click="dataSearchArea"
                >
                  Search
                </button>
    </div>

    <!-- Danh sách option -->
    <div class="dropdown-options" v-if="isOpen">
      <label v-for="item in DataArea" :key="item" class="dropdown-option">
        <input 
          type="checkbox" 
          :value="item" 
          v-model="dataAreaSearch"
        />
        {{ item }}
      </label>

      
    </div>

    
  </div>

      </div>
  
      <div class="zone-container">
        <!-- Loop through zones based on selected floor -->
        <div v-for="(zone, index) in zones" :key="index" class="zone-grid">
          <div class="zone-label">{{ zone.area }}</div>
          <div class="column" style="width: 400px; overflow-y: auto;">
            <div class="row" v-for="(row, indexRow) in zone.line" :key="indexRow">
              <div class="row-item">
                <button
                  class="row-label"
                  @click="openRowDetail(row.line, zone.area)"
                  :style="{ marginBottom: '15px' }"
                >
                  {{ indexRow + 1 }}
                </button>
                <div class="sub-row-container" style="width: 40px; overflow-x: auto; flex-wrap: wrap;">
                  <div
                    class="sub-row"
                    v-for="(box, indexBox) in row.shelf"
                    :key="indexBox"
                    
                  >
                  <button
                    v-if="box.isProduct == true"
                         @click="openFrameData(box.shelf, row.line, zone.area, indexBox + 1)"
                        style="background-color: red; color: white;"
                        class="box"
                        :class="{
                          'has-items': hasItems(zone, row, box),
                          empty: !hasItems(zone, row, box),
                        }"
                      >
                        <span>
                          {{ indexBox + 1 }}
                        </span>
                      </button>
  
                      <button
                    v-else
                         @click="openFrameData(box.shelf, row.line, zone.area, indexBox + 1)"
                        class="box"
                        :class="{
                          'has-items': hasItems(zone, row, box),
                          empty: !hasItems(zone, row, box),
                        }"
                      >
                        <span>
                          {{ indexBox + 1 }}
                        </span>
                      </button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
  
      <!-- Frame chi tiết vị trí -->
      <div v-if="frameVisible" class="detail-frame" style="width: 600px;">
        <div class="frame-content" >
          <div v-for="(item, index) in selectedDetail" :key="index">
            <div class="frame-top">
              
              <div class="frame-details">
                <table  class="table" style="width: 400px;">
              <thead>
                <tr>
                  <th class="title" v-if="dataShowAreaLine.area.trim()">Area: {{ dataShowAreaLine.area }}</th>
                  <th class="title" v-if="dataShowAreaLine.line.trim()">Line:{{ dataShowAreaLine.line }}</th>
                  <th class="title" v-if="dataShowAreaLine.shelf.trim()">Shelf:{{ dataShowAreaLine.shelf }}</th>
                  <th class="title" v-if="dataShowAreaLine.location.trim()">Location:{{ dataShowAreaLine.location }}</th>
                </tr>
              </thead>
            </table>
            

            <table  class="table" style="width: 450px;">
              <thead>
                <tr>
                  <th class="title">產品</th>
                  <th class="title">類別</th>
                  <th class="title">供應商</th>
                  <th class="title">數量位置</th>
                </tr>
              </thead>

              <tbody>
                <tr @click="showData(item.idProduct)">
                  <td>{{ item.title }}</td>
                  <td>{{ item.supplier }}</td>
                  <td>{{ item.location }}</td>
                  <td>{{ item.quantity }}</td>
                </tr>
              </tbody>
            </table>
              </div>
            </div>
          </div>
          
  
          <div class="frame-buttons">
            <button class="close-button" @click="frameVisible = false">
              關閉按鈕
            </button>
          </div>
        </div>
      </div>
  
      <!-- Frame danh sách sản phẩm -->
      <div v-if="rowDetailVisible" class="detail-frame" style="overflow-x: auto; height: 300px;">
        <div style="position: absolute; right: 10px; top: 10px;">
          <button class="close-button" @click="rowDetailVisible = false">
              關閉按鈕
            </button>
        </div>
        <div class="frame-content" style="display: flex; flex-wrap: wrap; ">
          <table  class="table" style="width: 300px;">
              <thead>
                <tr>
                  <th class="title" v-if="dataShowAreaLine.area.trim()">Area: {{ dataShowAreaLine.area }}</th>
                  <th class="title" v-if="dataShowAreaLine.line.trim()">Line:{{ dataShowAreaLine.line }}</th>
                  <th class="title" v-if="dataShowAreaLine.shelf.trim()">Shelf:{{ dataShowAreaLine.shelf }}</th>
                </tr>
              </thead>
            </table>
          <div style="width: 100%; display: flex; justify-content: center;">
          <input type="text" @input="SearchDataFram1" placeholder="Search Product, Location" v-model="searcDataFram" style="padding: 5px 5px; border-radius: 10px; border: 1px dashed greenyellow;">
          <button class="btn" style="border: 1px solid greenyellow; margin: 0 10px;" @click="SearchDataFram1">搜尋</button>
          
        </div>
          <table class="table">
        <thead>
          <tr>
            <th class="title">產品</th>
            <th class="title">類別</th>
            <th class="title">供應商</th>
            <th class="title">價格</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in selectedRowDetail" :key="index" @click="showData(item.idProduct)">
                <td v-html="highlightText(item.title)"></td>
                <td v-html="highlightText(item.location)"></td>
                <td >{{ item.supplier }}</td>
                <td>{{ item.quantity }}</td>
              </tr>
        </tbody>
      </table>
          
          
        </div>
      </div>
    </div>
    <PagesTotal :page="page" :totalPage="totalPage" :valueE="valueE" @pageChange="dataSearchArea" @pageSizeChange="changeReload"></PagesTotal>
    <!-- Hiển thị màn hình loading -->
    <div v-if="isLoading" class="loading-overlay">
      <div class="spinner"></div>
      <p>Loading...</p>
    </div>
  
     <!-- Form hiển thị khi isFormVisible = true -->
     <div v-if="isFormVisible && dataShelfLocation" class="form-container2">
      
        <div class="form-content2" style="width: 600px;">
          <div>
            <table  class="table" style="width: 300px;">
              <thead>
                <tr>
                  <th class="title" v-if="dataShowAreaLine.area.trim()">Area: {{ dataShowAreaLine.area }}</th>
                  <th class="title" v-if="dataShowAreaLine.line.trim()">Line:{{ dataShowAreaLine.line }}</th>
                  <th class="title" v-if="dataShowAreaLine.shelf.trim()">Shelf:{{ dataShowAreaLine.shelf }}</th>
                </tr>
              </thead>
            </table>
            <!-- <p style="font-weight: bold;">
              {{ dataShowAreaLine.area }} => {{ dataShowAreaLine.line }} {{ dataShowAreaLine.shelf != '' ? '=>' + dataShowAreaLine.shelf : '' }}
            </p> -->
            <button class="row-label"
                  @click="openRowDetailShelf(dataShelfLocation.productbyShelf)"
                  :style="{ marginBottom: '15px' }">{{ indexLine }}</button>
        <button @click="isFormVisible = false" style="border: none; outline: none; background-color: transparent; color: red;"><i class="fa fa-window-close-o" aria-hidden="true"></i></button>
          </div>
          <div style="display: flex; flex-wrap: wrap;">
            <div v-for="item in 5" :key="item" style="display: flex;">
              <div v-for="itemy in 6" :key="itemy">
                <div v-if="dataShelfLocation.productbyShelf?.some(x => x.location.slice(-2) == item + '' + itemy)">
                  <p v-if="dataShelfLocation?.findAllPlanDatas?.some(x => (x.locationOld.slice(-2) == item + '' + itemy && x.areaOld == dataShelfLocation.area && x.lineOld == dataShelfLocation.line && x.shelfOld == dataShelfLocation.shelf) 
                  || (x.locationNew.slice(-2) == item + '' + itemy && x.areaNew == dataShelfLocation.area && x.lineNew == dataShelfLocation.line && x.shelfNew == dataShelfLocation.shelf)) 
                  && findOnePlan(dataShelfLocation.findAllPlanDatas, item + '' + itemy, dataShelfLocation.area, dataShelfLocation.line, dataShelfLocation.shelf) != null" 
                  style="position: absolute; font-weight: bold; font-size: 10px; animation: index1 0.5s ease-in-out infinite;">
                  {{ "plan :" +  findOnePlan(dataShelfLocation.findAllPlanDatas, item + '' + itemy, dataShelfLocation.area, dataShelfLocation.line, dataShelfLocation.shelf).id }}, type: {{ findOnePlan(dataShelfLocation?.findAllPlanDatas, item + '' + itemy, dataShelfLocation.area, dataShelfLocation.line, dataShelfLocation.shelf).locationOld == dataShelfLocation.area + dataShelfLocation.line + dataShelfLocation.shelf + item + '' + itemy ? "Old" : "New" }}
                </p>
                  <button
                        style="background-color: blueviolet; color: white; padding: 15px 35px; margin: 5px 5px;"
                        class="box2"
                        @click="
                          openFrame(
                            dataShelfLocation.productbyShelf,
                            item + '' + itemy
                          )
                        "
                      >
                      
                      <span>
                        {{ item  + '-' + itemy}}
                      </span>
                      </button>
                </div>
                <div v-else>
                  <p v-if="dataShelfLocation?.findAllPlanDatas?.some(x => (x.locationOld.slice(-2) == item + '' + itemy && x.areaOld == dataShelfLocation.area && x.lineOld == dataShelfLocation.line && x.shelfOld == dataShelfLocation.shelf) 
                  || (x.locationNew.slice(-2) == item + '' + itemy && x.areaNew == dataShelfLocation.area && x.lineNew == dataShelfLocation.line && x.shelfNew == dataShelfLocation.shelf)) 
                  && findOnePlan(dataShelfLocation.findAllPlanDatas, item + '' + itemy, dataShelfLocation.area, dataShelfLocation.line, dataShelfLocation.shelf) != null" 
                  style="position: absolute; font-weight: bold; font-size: 10px; animation: index1 0.5s ease-in-out infinite;">
                  {{ "plan :" +  findOnePlan(dataShelfLocation.findAllPlanDatas, item + '' + itemy, dataShelfLocation.area, dataShelfLocation.line, dataShelfLocation.shelf).id }}, type: {{ findOnePlan(dataShelfLocation?.findAllPlanDatas, item + '' + itemy, dataShelfLocation.area, dataShelfLocation.line, dataShelfLocation.shelf).locationOld == dataShelfLocation.area + dataShelfLocation.line + dataShelfLocation.shelf + item + '' + itemy ? "Old" : "New" }}
                </p>

                  <button
                       
                        style="color: black; padding: 15px 35px; margin: 5px 5px;"
                        class="box2"
                        @click="
                          openFrame(
                            dataShelfLocation.productbyShelf,
                            item + '' + itemy
                          )
                        "
                      >
                      
                      <span>
                        {{ item  + '-' + itemy}}
                      </span>
                      </button>
                </div>
              </div>
            </div>
          </div>
          
          
        </div>
      </div>

      <div v-if="frameVisibleNew" class="frame-popup" >
        <div
          class="frame-content"
          :style="{ maxWidth: '800' + 'px', justifyContent: 'flex-start' }"
        >
        
          <div
            class="frame-item"
            
            style="margin: 10px 50px"
          >
            <h3>{{ DataframeData?.title }}</h3>
            
            <div class="frame-info">
              <div class="info-line" style="display: flex; justify-content: center;">
                <p style="font-weight: bold;">廠商: <span>{{ DataframeData?.supplier }}</span></p> 
                <p style="font-weight: bold;">數量: <span>{{ DataframeData?.quantity }}</span></p> 
                
              </div>
              <!-- <button @click="closeFrame" v-if="item.id_plan == 0" class="close-btn">Swap</button> -->
            </div>

            <div style="position: absolute; top: 10px; right: 10px;">
              <button class="btn" style="border: 1px solid black; background-color: cornflowerblue; color: white;" @click="closeFaram">
          關閉
        </button>
            </div>
            
        <div>
          <div style="display: flex; width: 100%; justify-content: center; margin: 15px 0;">
            <h2 style="margin: 0 20px;" v-if="DataframeData?.history?.length > 0 || DataframeData.inOutByProducts.length > 0">儲位歷史記錄</h2>
            <button class="btn" @click="dowloadExcelOneData(DataframeData.title)" style="border: 1px solid black;">
          下載Excel
        </button>
          </div>
          <div v-if="DataframeData?.history?.length > 0">
              
              <table class="table">
        <thead>
          <tr>
            <th class="title">區域</th>
            <th class="title">排</th>
            <th class="title">架</th>
            <th class="title">儲位代碼</th>
            <th class="title">時間</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(itemProduct, indexProduct) in DataframeData?.history" :key="indexProduct">
            <td>{{ itemProduct.location_old.area}}</td>
            <td>{{ itemProduct.location_old.line }}</td>
            <td>{{ itemProduct.location_old.shelf }}</td>
            <td>{{ itemProduct.location_old.code_location_addr }}</td>
            <td>{{ formatDateTime(itemProduct.time) }}</td>
          </tr>
        </tbody>
      </table>
            </div>

            <div v-if="DataframeData.inOutByProducts.length > 0">
              <table class="table">
        <thead>
          <tr>
            <th class="title">狀態</th>
            <th class="title">位置</th>
            <th class="title">更新時間</th>
            <th class="title">數量</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(itemProduct, indexProduct) in DataframeData.inOutByProducts" :key="indexProduct">
            <td>{{ itemProduct.status == 0 ? "出庫" : "入庫" }}</td>
            <td>{{ itemProduct.location }}</td>
            <td>{{ formatDateTime(itemProduct.updateat) }}</td>
            <td>{{ itemProduct.quantity }}</td>
            
          </tr>
        </tbody>
      </table>
            </div>
        </div>
          </div>
        </div>
      </div>
  </template>
  
  <script setup>
  // import { useCounterStore } from "../store";
  import { ref, getCurrentInstance, onMounted, watch } from "vue";
  import PagesTotal from './PageList/PagesTotal.vue'
  import axios from "axios";
  import { useToast } from "vue-toastification";
  
  // Updated floors and zones, fixed to correctly match the zones for each floor
  const zones = ref([]);
  const valueE = ref("")
  const frameVisible = ref(false);
  const rowDetailVisible = ref(false);
  const selectedDetail = ref([]);
  const selectedRowDetail = ref([]);
  const isLoading = ref(false);
  const searcDataFram = ref("")
  const isFormVisible = ref(false)
  const page = ref(1)
    const totalPage = ref(0)
    const pageSize = ref(2)
    const dataShelfLocation = ref([])
    const selectedRowDetail2 = ref([])
  
  const { proxy } = getCurrentInstance();
  const hostName = proxy?.hostname;
  // const store = useCounterStore();
  const indexLine = ref(0)
  const Toast = useToast();
  const DataArea = ref([])
  const dataAreaSearch = ref([])
  const isOpen = ref(false)
  const dataShowAreaLine = ref({
    line: '',
    shelf: '',
    area: '',
    location: ''
  })
  const dataDemo = ref({
    line: '',
    area: ''
  })

  const dataSearchAreaData = ref({
    data: [],
    page: 1,
    pageSize: 20
  })

  const frameVisibleNew = ref(false)
  const DataframeData = ref([])
  // Sample inventory data
  const inventory = ref({
    "C1-2-3": "Sản phẩm A",
    "C2-5-7": "Sản phẩm C",
    "C3-8-4": "Sản phẩm D",
    "C4-3-6": "Sản phẩm G",
    "C5-9-2": "Sản phẩm H",
  });
  
  onMounted(() => {
    // findOneAreByFloor(valueE.value, page.value)
    dataArea()
  });

  const closeFaram = () => {
    frameVisibleNew.value = !frameVisibleNew.value
  }
  
  const formatDateTime = (dateTimeString) => {
  const date = new Date(dateTimeString);

  const day = date.getDate().toString().padStart(2, "0");
  const month = (date.getMonth() + 1).toString().padStart(2, "0"); // Tháng bắt đầu từ 0
  const year = date.getFullYear();

  const hours = date.getHours().toString().padStart(2, "0");
  const minutes = date.getMinutes().toString().padStart(2, "0");

  // return `${day}/${month}/${year} ${hours}:${minutes}`;
  return `${year}/${month}/${day}/ ${hours}:${minutes}`;
};
  const dowloadExcelOneData = async (title) => {
    isLoading.value = true;
    document.body.classList.add("loading"); // Add Lớp "loading"
    document.body.style.overflow = "hidden";
    const res = await axios.post(hostName + `/api/Product/FindAllDownLoadExcelByCodeProduct?code=${title}`, {}, {
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

  const findOnePlan = (item, location, area, line, shelf) => {
    
    return item.find(x => ((x.locationOld.slice(-2) == location && x.areaOld == area 
    && x.lineOld == line && x.shelfOld == shelf))
               || ((x.locationNew.slice(-2) == location && x.areaNew == area 
               && x.lineNew == line && x.shelfNew == shelf)))
    
    // return item.find(x => x.locationOld.slice(-2) == location || x.locationNew.slice(-2) == location)
  }

  const dataSearchArea = async (search, pageData) => {
    console.log(search)
    isLoading.value = true;
    document.body.classList.add("loading"); // Add Lớp "loading"
    document.body.style.overflow = "hidden";
    dataSearchAreaData.value.data = dataAreaSearch.value
    dataSearchAreaData.value.page = pageData
    dataSearchAreaData.value.pageSize = pageSize.value

    const res = await axios.post(hostName + `/api/location_addr/FindAllDataDashBoadSearch`, dataSearchAreaData.value)
    if (res.data.success) {
      if(res.data.content.data.length > 0){
        page.value = res.data.content.page
        totalPage.value = res.data.content.totalPages
        zones.value = res.data.content.data;
        if(page.value > totalPage.value)
          page.value = 1
        Toast.success("Success");
      }else{
        
        zones.value = []
      }
      
    }else{
        zones.value = []
    }

    isLoading.value = false;
    document.body.classList.remove("loading");
    document.body.style.overflow = "auto";
  }
  const dataArea = async () => {
    isLoading.value = true;
    document.body.classList.add("loading"); // Add Lớp "loading"
    document.body.style.overflow = "hidden";
    const res = await axios.get(hostName + `/api/location_addr/FindAllData`)
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

  const toggleDropdowns = () => {
      isOpen.value = !isOpen.value;
    };
  const FilterData = () => {
     return  selectedRowDetail2.value.filter(item => {
      return (
        item.title.toLowerCase().includes(searcDataFram.value.toLowerCase()) 
        || item.location.toLowerCase().includes(searcDataFram.value.toLowerCase()) 
      )
    })
    
  }

  const openRowDetailShelf = (zone) => {
    selectedRowDetail2.value = []
  selectedRowDetail.value = [];
  if(zone.length <= 0)
    return
  zone.forEach((element) => {
    selectedRowDetail.value.push(element);
    selectedRowDetail2.value.push(element)
  });

  rowDetailVisible.value = true;
};
  const highlightText = (text) => {
      if (!searcDataFram.value.trim()) return text; // Không có tìm kiếm, hiển thị nguyên gốc

      const escapedSearchText = searcDataFram.value.replace(/\\/g, "\\\\"); // Escape "\" trước khi đưa vào RegExp bằng cách thay thế tất cả "\" thành "\\".
      const regex = new RegExp(`(${escapedSearchText})`, "gi"); // Tạo regex tìm kiếm không phân biệt hoa/thường
      return text.replace(regex, `<span class="highlight" style="padding: 2px;font-weight: bold;background-color: yellow;">$1</span>`); // Bọc đoạn trùng bằng thẻ <span>
    }
  const SearchDataFram1 = () => {
    if(!searcDataFram.value.trim()){
      selectedRowDetail.value = selectedRowDetail2.value
      return
    }

    isLoading.value = true
      document.body.classList.add('loading') // Add Lớp "loading"
      document.body.style.overflow = 'hidden'

      selectedRowDetail.value = FilterData()
      isLoading.value = false
      document.body.classList.remove('loading')
      document.body.style.overflow = 'auto'
  }

  const showData = async (id) => {
    isLoading.value = true;
    document.body.classList.add("loading"); // Add Lớp "loading"
    document.body.style.overflow = "hidden";
    const res = await axios.get(hostName + `/api/Product/findOneByOutAndIn?id=${id}`)

    if(res.data.success){
      frameVisibleNew.value = true
      DataframeData.value = res.data.content
      console.log(res)
    }

    console.log(res)
    isLoading.value = false;
    document.body.classList.remove("loading");
    document.body.style.overflow = "auto";
  }
  const openFrameData = async (shelf, line, area, index) => {
    console.log(area)
    indexLine.value = index

    dataShowAreaLine.value.line = ''
      dataShowAreaLine.value.area = ''
      dataShowAreaLine.value.shelf = ''
    isLoading.value = true
      document.body.classList.add('loading') // Add Lớp "loading"
      document.body.style.overflow = 'hidden'

    const res = await axios.get(hostName + `/api/location_addr/FindAllDataLocation?line=${line}&area=${area}&shelf=${shelf}`)
    if(res.data.success){
      console.log(res)
      dataShelfLocation.value = res.data.content

      dataShelfLocation.value = {...dataShelfLocation.value, area: area, line: line, shelf: shelf}
      dataShelfLocation.value.location = [...dataShelfLocation.value.location].sort((a, b) => {
        const numA = parseInt(a.slice(-2))
        const numB = parseInt(b.slice(-2))

        return numA - numB
      })
      isFormVisible.value = true 

      dataShowAreaLine.value.line = line
      dataShowAreaLine.value.area = area
      dataShowAreaLine.value.shelf = shelf
    }

    console.log(dataShelfLocation.value)
    isLoading.value = false
    
      document.body.classList.remove('loading')
      document.body.style.overflow = 'auto'
    
  }

  // const findOneAreByFloor = async (search, pageData) => {
  //   console.log(search)
  //   isLoading.value = true
  //     document.body.classList.add('loading') // Add Lớp "loading"
  //     document.body.style.overflow = 'hidden'
  //   const res = await axios.get(
  //     hostName +
  //       `/api/location_addr/FindAllDataDashBoad?page=${pageData}&pageSize=${pageSize.value}`,
  //     getToken()
  //   );
  //   console.log(res);
  //   if (res.data.success) {
  //     if(res.data.content.data.length > 0){
  //       page.value = res.data.content.page
  //       totalPage.value = res.data.content.totalPages
  //       zones.value = res.data.content.data;
  //       if(page.value > totalPage.value)
  //         page.value = 1
  //       Toast.success("Success");
  //     }else{
        
  //       zones.value = []
  //     }
      
  //   }else{
  //       zones.value = []
  //   }
  
  //   isLoading.value = false
  //     document.body.classList.remove('loading')
  //     document.body.style.overflow = 'auto'
  // };
  // Check if a box has items in the inventory
  const hasItems = (zone, row, col) => {
    return Boolean(inventory.value[`${zone}-${row}-${col}`]);
  };
  
  // Open frame with detailed item info
  const openFrame = (list, location) => {
    dataShowAreaLine.value.location = ''
    selectedDetail.value = [];
    const checkListData = list.filter((x) => x.location.slice(-2) == location);
    if (checkListData.length <= 0) return;
    if (list.length > 0) {
      if (checkListData.length > 0) {
        checkListData.forEach((element) => {
          if (element.location.slice(-2) === location) {
            dataShowAreaLine.value.location = location
            let dataItem = { ...element, type: "Sản phẩm đang ở kho"};
            selectedDetail.value.push(dataItem);
          }
        });
      }
    }
    frameVisible.value = true;
  };
  
  // Open row detail to show the products in a specific row
  const openRowDetail = async (line, area) => {
    selectedRowDetail.value = [];
    selectedRowDetail2.value = []
    dataShowAreaLine.value.line = ''
      dataShowAreaLine.value.area = ''
      dataShowAreaLine.value.shelf = ''

    isLoading.value = true
      document.body.classList.add('loading') // Add Lớp "loading"
      document.body.style.overflow = 'hidden'

    const res = await axios.get(hostName + `/api/location_addr/FindAllDataShelf?line=${line}&area=${area}`)
    if(res.data.success){
      console.log(res)
      res.data.content.forEach(e => {
        e.productbyShelf.forEach(e1 => {
          selectedRowDetail.value.push(e1)
          selectedRowDetail2.value.push(e1)
        })
      })

      dataShowAreaLine.value.line = line
      dataShowAreaLine.value.area = area
      dataDemo.value.line = line
      dataDemo.value.area = area
      rowDetailVisible.value = true;
    }

    isLoading.value = false
      document.body.classList.remove('loading')
      document.body.style.overflow = 'auto'
    
  };
  
  const changeReload = (event) => {
      pageSize.value = event
      dataSearchArea(valueE.value, page.value)
    }
  
    watch(page.value, (newPage) => {
      dataSearchArea(valueE.value, newPage)
    })
  
  
  </script>
  
  <style>
    @keyframes AlmostFull {
      0%{
        color: blue;
      }
      100%{
        color: azure;
      }
    }
  
    @keyframes Full {
      0%{
        color: goldenrod;
      }
      50%{
        color: white;
      }
      100%{
        color: green;
      }
    }
  
    @keyframes AlmostFullHeader {
      0%{
        background-color: violet;
      }
      100%{
        background-color: darkorange;
      }
    }
  
    @keyframes FullHeader {
      0%{
        background-color: gold;
      }
      100%{
        background-color: mediumblue;
      }
    }
  </style>
  
  <style scoped>
  /* Kiểu cho dropdown */
  /* CSS cơ bản cho dropdown */
.dropdown-container {
  position: relative;
  width: 250px;
}

.dropdown-select {
  padding: 10px;
  border: 1px solid #ccc;
  background: white;
  cursor: pointer;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.arrow {
  font-size: 14px;
}

.dropdown-options {
  position: absolute;
  top: 100%;
  left: 0;
  width: 100%;
  background: white;
  border: 1px solid #ccc;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
  max-height: 200px;
  overflow-y: auto;
}

.dropdown-option {
  display: flex;
  align-items: center;
  padding: 5px;
  cursor: pointer;
}

.dropdown-option input {
  margin-right: 10px;
}
        
.highlight {
  background-color: yellow;
  font-weight: bold;
  padding: 2px;
}
  /* Container chính */
  .container {
    max-width: 1000px;
    margin: auto;
    text-align: center;
    background: #ffffff;
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0px 4px 15px rgba(0, 0, 0, 0.15);
  }
  
  /* Header */
  .header {
    display: flex;
    align-items: center;
    gap: 10px;
    justify-content: start;
    margin-bottom: 20px;
  }
  
  /* Select Box */
  /* Cải thiện kiểu cho select */
  .small-select {
    width: 120px; /* Tăng chiều rộng */
    height: 35px;
    padding: 5px;
    border: 1px solid #4a90e2;
    border-radius: 5px;
    background: linear-gradient(135deg, #ffffff, #f1f4ff);
    transition: all 0.3s ease;
    font-size: 14px;
  }
  
  .small-select:hover {
    background: linear-gradient(135deg, #e8eeff, #cbd9ff);
  }
  
  .small-select:focus {
    border-color: #2c5fb8;
    box-shadow: 0 0 8px rgba(44, 95, 184, 0.5);
  }
  
  /* Cải thiện zone-label */
  .zone-label {
    margin-bottom: 10px;
    font-weight: bold;
    background: linear-gradient(135deg, #3a7bd5, #d76d77);
    font-size: 14px; /* Thu nhỏ kích thước font */
    text-transform: uppercase;
    display: inline-block;
    text-align: center;
    width: 100%;
    background-color: #f8f9fa;
    border-radius: 5px;
    padding: 5px 10px;
    margin-top: 10px;
    box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
    margin-bottom: 20px;
  }
  
  /* Cải thiện zone-container */
  .zone-container {
    display: grid;
    grid-template-columns: repeat(
      auto-fit,
      minmax(300px, 1fr)
    ); /* Cải thiện layout */
    gap: 30px;
    justify-content: center;
    margin-top: 30px;
  }
  
  /* Cải thiện zone-grid */
  .zone-grid {
    display: flex;
    flex-direction: column;
    align-items: center;
    background: #f8f9fa;
    padding: 15px;
    border-radius: 10px;
    border: 1px solid #ddd;
    box-shadow: 4px 4px 10px rgba(0, 0, 0, 0.1);
    transition: all 0.3s ease-in-out;
    width: 100%; /* Đảm bảo mỗi zone-grid có chiều rộng đầy đủ */
  }
  
  .zone-grid:hover {
    transform: translateY(-5px);
    box-shadow: 6px 6px 15px rgba(0, 0, 0, 0.2);
  }
  
  .small-select:hover {
    background: linear-gradient(135deg, #e8eeff, #cbd9ff);
  }
  
  .small-select:focus {
    border-color: #2c5fb8;
    box-shadow: 0 0 8px rgba(44, 95, 184, 0.5);
  }
  
  /* Khu vực chứa các zone */
  .zone-container {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
    gap: 50px;
    justify-content: center;
  }
  
  /* Mỗi zone */
  .zone-grid {
    display: flex;
    flex-direction: column;
    align-items: center;
    background: #f8f9fa;
    padding: 15px;
    border-radius: 10px;
    border: 1px solid #ddd;
    box-shadow: 4px 4px 10px rgba(0, 0, 0, 0.1);
    transition: all 0.3s ease-in-out;
  }
  
  .zone-grid:hover {
    transform: translateY(-5px);
    box-shadow: 6px 6px 15px rgba(0, 0, 0, 0.2);
  }
  
  /* Các hàng và cột */
  .column {
    display: flex;
    flex-direction: row;
    gap: 15px;
  }
  
  .row {
    display: flex;
    flex-direction: row;
    gap: 15px; /* Khoảng cách giữa các row-label */
    justify-content: center; /* Căn giữa các row-label */
  }
  .row-item {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  
  .sub-row-container {
    display: flex;
    flex-direction: row;
    gap: 2px;
  }
  
  .sub-row {
    display: flex;
    flex-direction: column;
    gap: 10px;
  }
  
  .box2 {
    border: 1px solid #6c757d;
    cursor: pointer;
    font-size: 8px;
    border-radius: 4px;
    transition: all 0.3s ease-in-out;
    box-shadow: 1px 1px 3px rgba(0, 0, 0, 0.1);
  }
  
  .box2:hover {
    background: linear-gradient(135deg, #d9e2ff, #c3d1ff);
    border-color: #4a90e2;
    box-shadow: 2px 2px 5px rgba(74, 144, 226, 0.4);
    transform: scale(1.2);
  }

  /* Ô box */

  .box {
    width: 12px;
    height: 12px;
    border: 1px solid #6c757d;
    background: linear-gradient(135deg, #ffffff, #f8f9fa);
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 8px;
    border-radius: 4px;
    padding: 0;
    margin: 1px;
    transition: all 0.3s ease-in-out;
    box-shadow: 1px 1px 3px rgba(0, 0, 0, 0.1);
  }
  
  .box:hover {
    background: linear-gradient(135deg, #d9e2ff, #c3d1ff);
    border-color: #4a90e2;
    box-shadow: 2px 2px 5px rgba(74, 144, 226, 0.4);
    transform: scale(1.2);
  }
  
  .box:active {
    background: linear-gradient(135deg, #4a90e2, #6b9df2);
    color: white;
    border-color: #2c5fb8;
    transform: scale(1);
  }
  
  /* Hiệu ứng khi được chọn */
  .box.selected {
    background: linear-gradient(135deg, #ff7675, #ff9999);
    color: white;
    border-color: #d63031;
    box-shadow: 2px 2px 5px rgba(214, 48, 49, 0.4);
  }
  .box.has-items {
    background: red;
    border: 1px solid darkred;
  }
  .box.empty {
    background: white;
    border: 1px solid grey;
  }
  .row-label {
    background: linear-gradient(135deg, #ff9a9e, #ff6a00);
    color: white;
    padding: 5px 9px;
    border-radius: 5px;
    cursor: pointer;
    box-sizing: border-box;
  }
  /* Nhãn của từng zone */
  .zone-label {
    margin-top: 5px;
    font-weight: bold;
    background-color: orange;
    color: #333;
    font-size: 16px;
    text-transform: uppercase;
  }
  
  /* Frame chi tiết */
  /* Frame chi tiết */
  .detail-frame {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background: #ffffff;
    padding: 20px;
    box-shadow: 0px 4px 15px rgba(0, 0, 0, 0.3);
    border-radius: 10px;
    text-align: left; /* Canh trái */
    z-index: 9999; /* Giá trị lớn để đảm bảo nằm trên tất cả */
    width: 1000px;
    animation: fadeIn 0.3s ease-in-out;
    display: flex;
    flex-wrap: wrap;
  }

  .frame-popup{
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    border: 1px solid black;
    background: #ffffff;
    padding: 20px;
    box-shadow: 0px 4px 15px rgba(0, 0, 0, 0.3);
    border-radius: 10px;
    text-align: left; /* Canh trái */
    z-index: 9999; /* Giá trị lớn để đảm bảo nằm trên tất cả */
    animation: fadeIn 0.3s ease-in-out;
    display: flex;
    flex-wrap: wrap;
  }
  
  .frame-content {
    gap: 20px; /* Khoảng cách giữa các phần tử */
    padding: 20px;
  }
  
  .frame-top {
    display: flex;
    gap: 20px;
  }
  
  .frame-image img {
    width: 100px; /* Kích thước ảnh */
    height: 100px;
    object-fit: cover;
    border-radius: 10px;
  }
  
  
  
  .frame-buttons {
    margin-top: 15px;
    display: flex;
    justify-content: flex-end;
  }
  
  /* Hiệu ứng fadeIn */
  @keyframes fadeIn {
    from {
      opacity: 0;
      transform: translate(-50%, -55%);
    }
    to {
      opacity: 1;
      transform: translate(-50%, -50%);
    }
  }
  
  @keyframes fadeIn {
    from {
      opacity: 0;
      transform: translate(-50%, -55%);
    }
    to {
      opacity: 1;
      transform: translate(-50%, -50%);
    }
  }
  
  .frame-content {
    display: flex;
  }
  
  /* Hình ảnh trong frame */
  .frame-image {
    width: 150px;
    height: 150px;
    object-fit: cover;
    border-radius: 10px;
    margin-bottom: 10px;
    box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.2);
  }
  
  /* Các nút trong frame */
  .frame-buttons {
    margin-top: 15px;
    display: flex;
    gap: 10px;
  }
  
  .action-button {
    background: linear-gradient(135deg, #007bff, #0056b3);
    color: white;
    border: none;
    padding: 8px 12px;
    cursor: pointer;
    border-radius: 5px;
    transition: all 0.3s ease-in-out;
  }
  
  .action-button:hover {
    background: linear-gradient(135deg, #0056b3, #003d80);
    box-shadow: 2px 2px 10px rgba(0, 91, 255, 0.3);
  }
  
  .close-button {
    background-color: #4a90e2;
    color: white;
    border: none;
    padding: 8px 12px;
    cursor: pointer;
    border-radius: 5px;
    transition: all 0.3s ease-in-out;
  }
  
  .close-button:hover {
    background: linear-gradient(135deg, #a71d2a, #7a141e);
    box-shadow: 2px 2px 10px rgba(255, 0, 0, 0.3);
  }
  
  .form-container2 {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
  }
  
  .form-content2 {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background: white;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
    text-align: center;
    width: 300px;
  }

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
  