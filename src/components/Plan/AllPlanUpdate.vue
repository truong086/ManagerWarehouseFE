<template>
    <div class="container">
      <div class="button-group">
        <button class="all" @click="showData('all', 'all')">所有計劃</button>
        <button class="lichsu" @click="showData('lichsu', 'lichsu')">
          歷史
        </button>
        <button class="NoReceiver" @click="showData('NoReceiver', 'NoReceiver')">
            尚未指定接收者的計劃
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
            @click="submitDate"
            style="color: black;"
            class="bg-blue-600 text-red font-semibold py-2 rounded-lg hover:bg-blue-700 transition duration-300"
          >
            Dowload Excel
          </button>

          <button 
            @click="submitSearch('search')" style="background-color: black;"
            class="bg-blue-600 text-white font-semibold py-2 rounded-lg hover:bg-blue-700 transition duration-300"
          >
            Search Data
          </button>
        </div>
      </div>
      <div style="height: auto" class="result">
        <div v-for="(item, index) in currentPlanData" :key="index">
          <div
            class="warehouse-frame"
            v-if="item.status == 2"
            style="background-color: rgba(11, 176, 217, 0.2); text-align: center; "
          >
            <div class="warehouse-info" style="z-index: 1000; text-align: center; width: 500px; margin: 0 auto;">
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
                <a style="font-weight: bold; color: red">完成的</a>
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
                <a style="font-weight: bold; color: red">等待確認</a>
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
              Update
            </button>

            <button
              class="btn btn-location"
              style="margin-left: 50px; background-color: rgba(11, 176, 217, 0.8)"
              @click="deleteData(item.id)"
            >
              Delete
            </button>
            </div>
          </div>
        </div>
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
      <p>加載中...</p>
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
const typeData = ref(null)
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
  const submitDate = async () => {
    if (!datetimePlan.value.datefrom) {
      alert("Vui lòng chọn ngày!");
      return;
    }
  
    if (!datetimePlan.value.dateto) {
      alert("Vui lòng chọn ngày!");
      return;
    }
  
    // Chuyển đổi thành ISO 8601 (UTC)
    datetimePlan.value.datefrom = new Date(datetimePlan.value.datefrom).toISOString();
    datetimePlan.value.dateto = new Date(datetimePlan.value.dateto).toISOString();
  
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
          a.download = 'DataExcel.xlsx'; // Đặt tên file khi tải xuống
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
    isLoading.value = true;
    document.body.classList.add("loading"); // Add Lớp "loading"
    document.body.style.overflow = "hidden";
    if(typeData.value === 'search'){
      if (!datetimePlan.value.datefrom) {
      alert("Vui lòng chọn ngày!");
      return;
    }
  
    if (!datetimePlan.value.dateto) {
      alert("Vui lòng chọn ngày!");
      return;
    }

      datetimePlanDate.value.page = pageData
    datetimePlanDate.value.pageSize = pageSize.value
    datetimePlanDate.value.datefrom = datetimePlan.value.datefrom
    datetimePlanDate.value.dateto = datetimePlan.value.dateto
      const res = await axios.post(hostName + `/api/Plan/FindAllDataByDone`, datetimePlanDate.value)

    console.log(res)
    if (res.data.success) {
        page.value = res.data.content.page;
        totalPage.value = res.data.content.totalPages;
        currentPlanData.value = res.data.content.data;
      }else{
        page.value = 1
        totalPage.value = 0
        currentPlanData.value = []
      }
    }
    else{
      if (typePlan.value === "all") {
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
        page.value = res.data.content.page;
        totalPage.value = res.data.content.totalPages;
        currentPlanData.value = res.data.content.data;
      }else{
        page.value = 1
        totalPage.value = 0
        currentPlanData.value = []
      }
  
      console.log(res)
    } else if (typePlan.value === "lichsu") {
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
        page.value = res.data.content.page;
        totalPage.value = res.data.content.totalPages;
        currentPlanData.value = res.data.content.data;
      }else{
        page.value = 1
        totalPage.value = 0
        currentPlanData.value = []
      }
    } else if (typePlan.value === "NoReceiver") {
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
        page.value = res.data.content.page;
        totalPage.value = res.data.content.totalPages;
        currentPlanData.value = res.data.content.data;
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
    typeData.value = search
    findAllData(valueE.value, page.value)

  }
  const changeReload = (event) => {
    pageSize.value = event;
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
  
    document.querySelector("." + data).style.backgroundColor = "#45a049";
    document.querySelector("." + data).style.color = "white";
    isButton.value = data;
    typePlan.value = type;
  
    console.log(typePlan.value)
  
    findAllData(valueE.value, page.value);
  };
  
  </script>
  
  <style scoped>
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
  