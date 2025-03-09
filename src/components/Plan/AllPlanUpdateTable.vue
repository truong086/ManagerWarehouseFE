<template>
    <div class="container">
      <div class="button-group">
        <button class="all" @click="showData('all', 'all')">所有計劃</button>
        <button class="lichsu" @click="showData('lichsu', 'lichsu')">
          已完成
        </button>
        <button class="NoReceiver" @click="showData('NoReceiver', 'NoReceiver')">
          尚未完成的計畫
        </button>
      </div>
      <div class="p-4 bg-white shadow-lg rounded-xl w-full max-w-md mx-auto">
        <label class="block text-lg font-semibold text-gray-700 mb-2">選擇時間範圍：</label>
  
        <div class="flex flex-col gap-4" style="display: flex;">
          <div>
            <label class="text-gray-600 text-sm">從日期：</label>
            <input 
              type="datetime-local" 
              v-model="datetimePlan.datefrom"
              class="w-full px-3 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
            />
          </div>
  
          <div>
            <label class="text-gray-600 text-sm">到日期：</label>
            <input 
              type="datetime-local" 
              v-model="datetimePlan.dateto"
              class="w-full px-3 py-2 border rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
            />
          </div>
          
          <button 
            @click="submitSearch('search')" style="background-color: black; border-radius: 10px;"
            class="bg-blue-600 text-white font-semibold py-2 rounded-lg hover:bg-blue-700 transition duration-300"
          >
          搜尋資料
          </button>

          <button 
            @click="submitDate"
            style="color: black; border-radius: 10px;"
            class="bg-blue-600 text-red font-semibold py-2 rounded-lg hover:bg-blue-700 transition duration-300"
          >
          下載Excel
          </button>

          
        </div>
      </div>
      <div style="height: auto" class="result">
        <table class="table" v-if="currentPlanData.length > 0">
        <thead>
          <tr>
            <th class="title">計畫編號</th>
            <th class="title">舊區域</th>
            <th class="title">舊排</th>
            <th class="title">舊架</th>
            <th class="title">舊位置</th>
            <th class="title">新區域</th>
            <th class="title">新排</th>
            <th class="title">新架</th>
            <th class="title">新位置</th>
            <!-- <th class="title">舊儲位</th>
            <th class="title">新儲位</th> -->
            <th class="title">時間</th>
            <th class="title">狀態</th>
            <th class="title">功能</th>
            <!-- <th class="title">History</th> -->
          </tr>
        </thead>
        <tbody>
          <tr v-for="(itemData, indexData) in currentPlanData" :key="indexData">
            <td :style="{ backgroundColor: itemData.status == 0 ? 'rgba(247, 231, 5, 0.2)' : 'rgba(52, 199, 62, 0.2) ' }">{{ itemData.id }}</td>
            <td :style="{ backgroundColor: itemData.status == 0 ? 'rgba(247, 231, 5, 0.2)' : 'rgba(52, 199, 62, 0.2) ' }">{{ itemData.areaOld }}</td>
            <td :style="{ backgroundColor: itemData.status == 0 ? 'rgba(247, 231, 5, 0.2)' : 'rgba(52, 199, 62, 0.2) ' }">{{ itemData.lineOld }}</td>
            <td :style="{ backgroundColor: itemData.status == 0 ? 'rgba(247, 231, 5, 0.2)' : 'rgba(52, 199, 62, 0.2) ' }">{{ itemData.shelfOld }}</td>
            <td :style="{ backgroundColor: itemData.status == 0 ? 'rgba(247, 231, 5, 0.2)' : 'rgba(52, 199, 62, 0.2) ' }">{{ itemData.locationOld }}</td>
            <td :style="{ backgroundColor: itemData.status == 0 ? 'rgba(247, 231, 5, 0.2)' : 'rgba(52, 199, 62, 0.2) ' }">{{ itemData.areaNew }}</td>
            <td :style="{ backgroundColor: itemData.status == 0 ? 'rgba(247, 231, 5, 0.2)' : 'rgba(52, 199, 62, 0.2) ' }">{{ itemData.lineNew }}</td>
            <td :style="{ backgroundColor: itemData.status == 0 ? 'rgba(247, 231, 5, 0.2)' : 'rgba(52, 199, 62, 0.2) ' }">{{ itemData.shelfNew }}</td>
            <td :style="{ backgroundColor: itemData.status == 0 ? 'rgba(247, 231, 5, 0.2)' : 'rgba(52, 199, 62, 0.2) ' }">{{ itemData.locationNew }}</td>
            <td :style="{ backgroundColor: itemData.status == 0 ? 'rgba(247, 231, 5, 0.2)' : 'rgba(52, 199, 62, 0.2) ' }">{{ formatDateTime(itemData.updateat) }}</td>
            <td :style="{ backgroundColor: itemData.status == 0 ? 'rgba(247, 231, 5, 0.2)' : 'rgba(52, 199, 62, 0.2) ' }">{{ itemData.status === 0 ? '尚未完成' : '已完成' }}</td>
            <td :style="{ backgroundColor: itemData.status == 0 ? 'rgba(247, 231, 5, 0.2)' : 'rgba(52, 199, 62, 0.2) ' }">
                <div v-if="itemData.status != 1">
                    <button
                class="btn btn-location"
                style="background-color: rgba(11, 176, 217, 0.8)"
                @click="NextMap(itemData.id)"
                >
                修改計劃
                </button>

                <button
                class="btn btn-location"
                style="background-color: rgba(11, 176, 217, 0.8); margin-left: 10px;"
                @click="deleteData(itemData.id)"
                >
                刪除計劃
                </button>
            </div>
            </td>
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

            
          <!-- <div
            class="warehouse-frame"
            v-if="item.status == 2"
            style="background-color: rgba(11, 176, 217, 0.2); text-align: center; "
          >
            <div class="warehouse-info" style="z-index: 1000; text-align: center; width: 500px; margin: 0 auto;">
              <p style="font-weight: bold; font-size: 15px;">
              {{ item.id }}
            </p>
              <p>
                舊位置：{{ item.areaOld }} => {{ item.lineOld }} =>
                {{ item.shelfOld }} => {{ item.locationOld }} 
              </p>
              <p style="font-size: 20px">⏬</p>
              <p>
                新位置：{{ item.areaNew }} => {{ item.lineNew }} =>
                {{ item.shelfNew }} => {{ item.locationNew }}
              </p>
              <h5>
                狀態：
                <a style="font-weight: bold; color: red">已收到</a>
              </h5>

              <h5>
                <a style="font-weight: bold; color: red">{{ formatDateTime(item.updateat) }}</a>
              </h5>
            </div>
          </div>
  
          <div
            class="warehouse-frame"
            v-else-if="item.status == 1"
            style="background: rgba(52, 199, 62, 0.2); text-align: center;"
          >
          <div class="warehouse-info" style="z-index: 1000; text-align: center; width: 500px; margin: 0 auto;" >
            <p style="font-weight: bold; font-size: 15px;">
              {{ item.id }}
            </p>
              <p>
                舊位置：{{ item.areaOld }} => {{ item.lineOld }} =>
                {{ item.shelfOld }} => {{ item.locationOld }} 
              </p>
              <p style="font-size: 20px">⏬</p>
              <p>
                新位置：{{ item.areaNew }} => {{ item.lineNew }} =>
                {{ item.shelfNew }} => {{ item.locationNew }}
              </p>
              <h5>
                狀態：
                <a style="font-weight: bold; color: red">已完成</a>
              </h5>

              <h5>
                <a style="font-weight: bold; color: red">{{ formatDateTime(item.updateat) }}</a>
              </h5>
            </div>
          </div>
  
          <div
            class="warehouse-frame"
            v-else
            style="background-color: rgba(247, 231, 5, 0.2); text-align: center;"
          >
          <div class="warehouse-info" style="z-index: 1000; text-align: center; width: 500px; margin: 0 auto;">
            <p style="font-weight: bold; font-size: 15px;">
              {{ item.id }}
            </p>
              <p>
                舊位置：{{ item.areaOld }} => {{ item.lineOld }} =>
                {{ item.shelfOld }} => {{ item.locationOld }} 
              </p>
              <p style="font-size: 20px">⏬</p>
              <p>
                新位置：{{ item.areaNew }} => {{ item.lineNew }} =>
                {{ item.shelfNew }} => {{ item.locationNew }}
              </p>
              <h5>
                狀態：
                <a style="font-weight: bold; color: red">尚未完成</a>
              </h5>

              <h5>
                <a style="font-weight: bold; color: red">{{ formatDateTime(item.updateat) }}</a>
              </h5>
            </div>

            <div>
                <button
              class="btn btn-location"
              style="margin-left: 50px; background-color: rgba(11, 176, 217, 0.8)"
              @click="NextMap(item.id)"
            >
            修改計劃
            </button>

            <button
              class="btn btn-location"
              style="margin-left: 50px; background-color: rgba(11, 176, 217, 0.8)"
              @click="deleteData(item.id)"
            >
            刪除計劃
            </button>
            </div>
          </div> -->
          <!-- <div v-for="(item, index) in currentPlanData" :key="index">
        </div> -->
      </div>
      <PagesTotal
        :page="page"
        :totalPage="totalPage"
        :valueE="valueE"
        @pageChange="findAllData"
        @pageSizeChange="changeReload"
      ></PagesTotal>
    </div>
  
    <div v-if="isLoading" class="loading-overlay">
      <div class="spinner"></div>
      <p>載入中...</p>
    </div>
  </template>
  
  
  <script setup>
  import { useCounterStore } from "../../store";
  import { ref, getCurrentInstance, watch } from "vue";
  import PagesTotal from "../PageList/PagesTotal.vue";
  import axios from "axios";
  import { useToast } from "vue-toastification";
  import {useRouter} from 'vue-router'
  const valueE = ref("");
  const isLoading = ref(false);
  const store = useCounterStore();
  const Toast = useToast();
  const page = ref(1);
  const totalPage = ref(0);
  const pageSize = ref(2);
  const currentPlanData = ref([]);
  const { proxy } = getCurrentInstance();
  const hostName = proxy?.hostname;
  const isButton = ref("");
  const typePlan = ref("");
  const router = useRouter()
  const datetimePlan = ref({
    datefrom: "",
    dateto: ""
  })
// const typeData = ref(null)
  const datetimePlanDate = ref({
    datefrom: "",
    dateto: "",
    page: 1,
    pageSize: 20
  })
  const NextMap = (id) => {
  router.push({ path: "PlanUpdatePage", query: { id: id, name: "Update Plan" } });
};

const deleteData = async(id) => {
    if(confirm("確定要刪除")){
        const res = await axios.delete(hostName + `/api/Plan/Delete?id=${id}`)
        if(res.data.success){
            typePlan.value = "all"
            findAllData(valueE.value, page.value)
        }
    }
}

// Chuyển đổi thời gian về UTC+8
const convertToTaiwanTime = (localDateTime) => {
  const date = new Date(localDateTime);
  const taiwanTime = new Date(date.getTime() + 8 * 60 * 60 * 1000); // Cộng 8 giờ (UTC+8)
  return taiwanTime.toISOString(); // Chuyển về ISO format (YYYY-MM-DDTHH:mm:ss.sssZ)
};
  const submitDate = async () => {
    if (!datetimePlan.value.datefrom) {
      alert("請選擇日期!");
      return;
    }
  
    if (!datetimePlan.value.dateto) {
      alert("請選擇日期!");
      return;
    }
  
    if(store.getTypeData == ''){
      alert("No Data")
      return
    }
    // Chuyển đổi thành ISO 8601 (UTC)
    datetimePlan.value.datefrom = convertToTaiwanTime(datetimePlan.value.datefrom)
    datetimePlan.value.dateto = convertToTaiwanTime(datetimePlan.value.dateto)
  
    isLoading.value = true;
    document.body.classList.add("loading"); // Add Lớp "loading"
    document.body.style.overflow = "hidden";
  
    try {
      const response = await axios.post(hostName + "/api/Plan/FindAllDownLoadExcel", datetimePlan.value, {
        headers: {
                      'Accept': 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
                      'Content-Type': 'application/json'
                  },
                  responseType: 'blob' // Cực kỳ quan trọng! Không có sẽ bị lỗi file
      });
      console.log(response)
       // Tạo URL từ Blob
       const url = window.URL.createObjectURL(new Blob([response.data]));
          const a = document.createElement('a');
          a.href = url;
          a.download = getCurrentTimestamp(); // Đặt tên file khi tải xuống
          document.body.appendChild(a);
          a.click();
          a.remove();
          window.URL.revokeObjectURL(url);
    } catch (error) {
      console.error("Lỗi gửi dữ liệu:", error);
    }
  
    datetimePlan.value.datefrom = ""
    datetimePlan.value.dateto = ""
  
    isLoading.value = false;
    document.body.classList.remove("loading");
    document.body.style.overflow = "auto";
  };

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
  watch(page.value, (newPage) => {
    findAllData(valueE.value, newPage);
  });
  const getToken = () => {
    var token = store.getToken;
    var result = {
      headers: { Authorization: `Bearer ${token}` },
    };
    return result;
  };
  const findAllData = async (search, pageData) => {
    currentPlanData.value = []

    if(search === "search" && store.getTypeData === "all"){
      if (!datetimePlan.value.datefrom) {
        alert("請選擇日期!");
        page.value = 1
        totalPage.value = 0
        currentPlanData.value = []
        return;
      }
  
      if (!datetimePlan.value.dateto) {
        alert("請選擇日期!");
        page.value = 1
        totalPage.value = 0
        currentPlanData.value = []
        return;
      }

      isLoading.value = true;
      document.body.classList.add("loading"); // Add Lớp "loading"
      document.body.style.overflow = "hidden";

        datetimePlanDate.value.page = pageData
      datetimePlanDate.value.pageSize = pageSize.value
      datetimePlanDate.value.datefrom = convertToTaiwanTime(datetimePlan.value.datefrom)
      datetimePlanDate.value.dateto = convertToTaiwanTime(datetimePlan.value.dateto)

      // datetimePlanDate.value.datefrom = datetimePlan.value.datefrom
      // datetimePlanDate.value.dateto = datetimePlan.value.dateto
      const res = await axios.post(hostName + `/api/Plan/FindAllDataByNoDoneAndDone`, datetimePlanDate.value)

    if (res.data.success) {
        page.value = res.data.content.page;
        totalPage.value = res.data.content.totalPages;
        currentPlanData.value = res.data.content.data;
      }else{
        page.value = 1
        totalPage.value = 0
        currentPlanData.value = []
      }

      isLoading.value = false;
    document.body.classList.remove("loading");
    document.body.style.overflow = "auto";
    }
    else if(search === 'search' && store.getTypeData === "lichsu"){
      if (!datetimePlan.value.datefrom) {
        page.value = 1
        totalPage.value = 0
        currentPlanData.value = []
        alert("請選擇日期!");
        return;
      }
  
    if (!datetimePlan.value.dateto) {
      page.value = 1
        totalPage.value = 0
        currentPlanData.value = []
      alert("請選擇日期!");
      return;
    }

    isLoading.value = true;
    document.body.classList.add("loading"); // Add Lớp "loading"
    document.body.style.overflow = "hidden";

      datetimePlanDate.value.page = pageData
    datetimePlanDate.value.pageSize = pageSize.value
    datetimePlanDate.value.datefrom = convertToTaiwanTime(datetimePlan.value.datefrom)
      datetimePlanDate.value.dateto = convertToTaiwanTime(datetimePlan.value.dateto)
      const res = await axios.post(hostName + `/api/Plan/FindAllDataByDone`, datetimePlanDate.value)

    console.log(res)
    if (res.data.success) {
        page.value = res.data.content.page;
        totalPage.value = res.data.content.totalPages;
        if(page.value > totalPage.value)
            page.value = 1
        currentPlanData.value = res.data.content.data;
      }else{
        page.value = 1
        totalPage.value = 0
        currentPlanData.value = []
      }

      isLoading.value = false;
    document.body.classList.remove("loading");
    document.body.style.overflow = "auto";
    }

    else if(search === 'search' && store.getTypeData === "NoReceiver"){
      if (!datetimePlan.value.datefrom) {
        page.value = 1
        totalPage.value = 0
        currentPlanData.value = []
        alert("請選擇日期!");
        return;
      }
  
    if (!datetimePlan.value.dateto) {
      page.value = 1
        totalPage.value = 0
        currentPlanData.value = []
      alert("請選擇日期!");
      return;
    }

    isLoading.value = true;
    document.body.classList.add("loading"); // Add Lớp "loading"
    document.body.style.overflow = "hidden";

      datetimePlanDate.value.page = pageData
    datetimePlanDate.value.pageSize = pageSize.value
    datetimePlanDate.value.datefrom = convertToTaiwanTime(datetimePlan.value.datefrom)
      datetimePlanDate.value.dateto = convertToTaiwanTime(datetimePlan.value.dateto)
      const res = await axios.post(hostName + `/api/Plan/FindAllDataByNoDone`, datetimePlanDate.value)

    console.log(res)
    if (res.data.success) {
        page.value = res.data.content.page;
        totalPage.value = res.data.content.totalPages;
        if(page.value > totalPage.value)
            page.value = 1
        currentPlanData.value = res.data.content.data;
      }else{
        page.value = 1
        totalPage.value = 0
        currentPlanData.value = []
      }

      isLoading.value = false;
    document.body.classList.remove("loading");
    document.body.style.overflow = "auto";
    }
    else{
      
      isLoading.value = true;
    document.body.classList.add("loading"); // Add Lớp "loading"
    document.body.style.overflow = "hidden";
    
      if (store.getTypeData === "all") {
        
      const res =
        search === ""
          ? await axios.get(
              hostName +
                `/api/Plan/FindAllNoDoneAndDone?page=${pageData}&pageSize=${pageSize.value}`,
              getToken()
            )
          : await axios.get(
              hostName +
                `/api/Plan/FindAllNoDoneAndDone?name=${search}&page=${pageData}&pageSize=${pageSize.value}`,
              getToken()
            );
  
      if (res.data.success) {
        if(res.data.content.data.length > 0){
          page.value = res.data.content.page;
          totalPage.value = res.data.content.totalPages;
          if(page.value > totalPage.value)
              page.value = 1
          currentPlanData.value = res.data.content.data;
        }else{
          store.setTypeDatas(store.getTypeData)
          findAllData("h", 1)

          isLoading.value = true;
          document.body.classList.add("loading"); // Add Lớp "loading"
          document.body.style.overflow = "hidden";
        }
      }else{
        page.value = 1
        totalPage.value = 0
        currentPlanData.value = []
      }
  
      console.log(res)
    } else if (store.getTypeData === "lichsu") {
      const res =
        search === ""
          ? await axios.get(
              hostName +
                `/api/Plan/FindAllDone?page=${pageData}&pageSize=${pageSize.value}`,
              getToken()
            )
          : await axios.get(
              hostName +
                `/api/Plan/FindAllDone?name=${search}&page=${pageData}&pageSize=${pageSize.value}`,
              getToken()
            );
        console.log(res)
      if (res.data.success) {
        if(res.data.content.data.length > 0){
          page.value = res.data.content.page;
          totalPage.value = res.data.content.totalPages;
          if(page.value > totalPage.value)
              page.value = 1
          currentPlanData.value = res.data.content.data;
        }else{
          store.setTypeDatas(store.getTypeData)
          findAllData("h", 1)

          isLoading.value = true;
          document.body.classList.add("loading"); // Add Lớp "loading"
          document.body.style.overflow = "hidden";
        }
        
      }else{
        page.value = 1
        totalPage.value = 0
        currentPlanData.value = []
      }
    } else if (store.getTypeData === "NoReceiver") {
      const res =
        search === ""
          ? await axios.get(
              hostName +
                `/api/Plan/FindAll?page=${pageData}&pageSize=${pageSize.value}`,
              getToken()
            )
          : await axios.get(
              hostName +
                `/api/Plan/FindAll?name=${search}&page=${pageData}&pageSize=${pageSize.value}`,
              getToken()
            );
  
      if (res.data.success) {
        if(res.data.content.data.length > 0){
          page.value = res.data.content.page;
          totalPage.value = res.data.content.totalPages;
          if(page.value > totalPage.value)
              page.value = 1
          currentPlanData.value = res.data.content.data;
        }else{
          store.setTypeDatas(store.getTypeData)
          findAllData("h", 1)

          isLoading.value = true;
          document.body.classList.add("loading"); // Add Lớp "loading"
          document.body.style.overflow = "hidden";
        }
      }else{
        page.value = 1
        totalPage.value = 0
        currentPlanData.value = []
      }
    }
    }
    
    Toast.success("Success");
    isLoading.value = false;
    document.body.classList.remove("loading");
    document.body.style.overflow = "auto";
  };
  
  const submitSearch = async (search) => {
    valueE.value = search
    findAllData(valueE.value, page.value)

  }
  const changeReload = (event) => {
    pageSize.value = event;
    page.value = 1
    findAllData(valueE.value, page.value);
  };
  
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
  const showData = (data, type) => {
    if (isButton.value != "") {
      document.querySelector("." + isButton.value).style.backgroundColor =
        "white";
      document.querySelector("." + isButton.value).style.color = "black";
    }

    currentPlanData.value = []
  
    document.querySelector("." + data).style.backgroundColor = "#45a049";
    document.querySelector("." + data).style.color = "white";
    isButton.value = data;
    typePlan.value = type;
    valueE.value = ""
    store.setTypeDatas(type)
  
    datetimePlan.value.datefrom = ""
    datetimePlan.value.dateto = ""
    findAllData(valueE.value, page.value);
  };
  
  </script>
  
  <style scoped>
  .table-page {
  padding: 20px;
  font-family: 'Arial', sans-serif;
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
  .container {
    text-align: center;
    margin-top: 20px;
    background: linear-gradient(135deg, #f0f0f0, #dcdcdc);
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
  }
  .button-group {
    display: flex;
    justify-content: center;
    padding-top: 0;
  }
  .all, .lichsu, .NoReceiver {
    padding: 12px 20px;
    font-size: 18px;
    cursor: pointer;
    border: none;
    background: white;
    color: black;
    font-weight: bold;
    transition: all 0.3s ease;
    width: 400px;
  }
  button:hover {
    background: #45a049;
    transform: scale(1.1);
  }
  .result {
    margin-top: 20px;
    font-size: 20px;
    font-weight: bold;
    color: #222;
    padding: 15px;
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
  }
  
  /* Warehouse Frame */
  .warehouse-frame {
    width: 100%;
    padding: 15px;
    display: flex;
    align-items: center;
    background: #f9f9f9;
    margin-top: 2rem;
    border-radius: 8px;
    box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
  }
  
  .warehouse-img img {
    width: 150px;
    height: 150px;
    object-fit: cover;
    border-radius: 8px;
  }
  
  .warehouse-info {
    margin-left: 1rem;
  }
  
  .warehouse-info h5 {
    font-size: 1.25rem;
    color: #333;
  }
  
  .warehouse-info p {
    font-size: 0.875rem;
    color: #555;
  }
  
  .btn-location {
    background-color: #28a745;
    border: none;
    padding: 0.5rem 1rem;
    color: white;
    text-transform: uppercase;
    font-size: 0.875rem;
    cursor: pointer;
    border-radius: 4px;
    transition: background-color 0.3s ease;
  }
  
  .btn-location:hover {
    background-color: #218838;
  }
  </style>
  