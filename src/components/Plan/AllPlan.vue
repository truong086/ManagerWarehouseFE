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
          class="bg-blue-600 text-white font-semibold py-2 rounded-lg hover:bg-blue-700 transition duration-300"
        >
          提交
        </button>
      </div>
    </div>
    <div style="height: auto" class="result">
      <div v-for="(item, index) in currentPlanData" :key="index">
        <div
          class="warehouse-frame"
          v-if="item.isConfirmation && item.status !== '完成的'"
          style="background-color: rgba(11, 176, 217, 0.2)"
        >
          <div class="warehouse-info" style="z-index: 1000">
            <h2 style="font-weight: bold">計劃名稱：{{ item.title }}</h2>
            <p>
              舊位置：{{ item.warehouseOld }} => {{ item.floorOld }} =>
              {{ item.areaOld }} => {{ item.shelfOld }} => {{ item.localtionOld }}
            </p>
            <p style="font-size: 20px">⏬</p>
            <p>
              新位置：{{ item.warehouse }} => {{ item.floor }} =>
              {{ item.area }} => {{ item.shelf }} => {{ item.localtionNew }}
            </p>
            <h5>創建帳戶：{{ item.account_creatPlan }}</h5>
            <h5>
              狀態：
              <a style="font-weight: bold; color: red">{{ item.status }}</a>
            </h5>
            <h5>
              接收者：
              <a style="font-weight: bold; color: violet">{{
                item.receiver_name
              }}</a>
              <img
                v-if="item.receiver_image"
                :src="item.receiver_image"
                style="width: 30px; height: 30px; border-radius: 50%"
                alt=""
              />
            </h5>
            <h5>
              最近更新時間：
              <a style="font-weight: bold; color: violet">{{
                formatDateTime(item.updatedAt)
              }}</a>
            </h5>
          </div>
          <div>
            <button
              v-if="item.isConfirmation"
              class="btn btn-location"
              style="margin-left: 50px; background-color: rgba(11, 176, 217, 0.8)"
              @click="NextMap(item.id)"
            >
              詳情
            </button>
            <button
              v-else
              class="btn btn-location"
              style="margin-left: 50px; background-color: yellow; color: black"
              @click="NextMap(item.id)"
            >
              詳情
            </button>

            <button
              class="btn btn-location"
              style=" background-color: green; color: white; margin: 15px 0;"
              @click="Update(item.id)"
            >
              更新
            </button>

            <button
              class="btn btn-location"
              style=" background-color: red; color: black"
              @click="Delete(item.id)"
            >
              刪除
            </button>
          </div>
        </div>

        <div
          class="warehouse-frame"
          v-else-if="item.isConfirmation && item.status === '完成的'"
          :style="
            item.isConfirmation
              ? 'background: rgba(52, 199, 62, 0.2); '
              : 'background: rgba(247, 231, 5, 0.2);'
          "
        >
          <div class="warehouse-info" style="z-index: 1000">
            <h2 style="font-weight: bold">計劃名稱：{{ item.title }}</h2>
            <p>
              舊位置：{{ item.warehouseOld }} => {{ item.floorOld }} =>
              {{ item.areaOld }} => {{ item.shelfOld }} => {{ item.localtionOld }}
            </p>
            <p style="font-size: 20px">⏬</p>
            <p>
              新位置：{{ item.warehouse }} => {{ item.floor }} =>
              {{ item.area }} => {{ item.shelf }} => {{ item.localtionNew }}
            </p>
            <h5>創建帳戶：{{ item.account_creatPlan }}</h5>
            <h5>
              狀態：
              <a style="font-weight: bold; color: red">{{ item.status }}</a>
            </h5>
            <h5>
              接收者：
              <a style="font-weight: bold; color: violet">{{
                item.receiver_name
              }}</a>
              <img
                v-if="item.receiver_image"
                :src="item.receiver_image"
                style="width: 30px; height: 30px; border-radius: 50%"
                alt=""
              />
            </h5>
            <h5>
              最近更新時間：
              <a style="font-weight: bold; color: violet">{{
                formatDateTime(item.updatedAt)
              }}</a>
            </h5>
          </div>
          <button
            v-if="item.isConfirmation"
            class="btn btn-location"
            style="margin-left: 50px"
            @click="NextMap(item.id)"
          >
            詳情
          </button>
          <button
            v-else
            class="btn btn-location"
            style="margin-left: 50px; background-color: yellow; color: black"
            @click="NextMap(item.id)"
          >
            詳情
          </button>
        </div>

        <div
          class="warehouse-frame"
          v-else
          style="background-color: rgba(247, 231, 5, 0.2);"
        >
          <div class="warehouse-info" style="z-index: 1000; width: 1000px;">
            <h2 style="font-weight: bold">計劃名稱：{{ item.title }}</h2>
            <p>
              舊位置：{{ item.warehouseOld }} => {{ item.floorOld }} =>
              {{ item.areaOld }} => {{ item.shelfOld }} => {{ item.localtionOld }}
            </p>
            <p style="font-size: 20px">⏬</p>
            <p>
              新位置：{{ item.warehouse }} => {{ item.floor }} =>
              {{ item.area }} => {{ item.shelf }} => {{ item.localtionNew }}
            </p>
            <h5>創建帳戶：{{ item.account_creatPlan }}</h5>
            <h5>
              狀態：
              <a style="font-weight: bold; color: red">{{ item.status }}</a>
            </h5>
            <h5>
              接收者：
              <a style="font-weight: bold; color: violet">{{
                item.receiver_name
              }}</a>
              <img
                v-if="item.receiver_image"
                :src="item.receiver_image"
                style="width: 30px; height: 30px; border-radius: 50%"
                alt=""
              />
            </h5>
            <h5>
              最近更新時間：
              <a style="font-weight: bold; color: violet">{{
                formatDateTime(item.updatedAt).includes("01/01/1 08:06")
                  ? "尚未更新"
                  : formatDateTime(item.updatedAt)
              }}</a>
            </h5>
          </div>
          <div>
            <button
              v-if="item.isConfirmation"
              class="btn btn-location"
              @click="NextMap(item.id)"
            >
              詳情
            </button>
            <button
              v-else
              class="btn btn-location"
              style="background-color: yellow; color: black"
              @click="NextMap(item.id)"
            >
              詳情
            </button>

            <button
              class="btn btn-location"
              style=" background-color: green; color: white; margin: 15px 0;"
              @click="Update(item.id)"
            >
              更新
            </button>

            <button
              class="btn btn-location"
              style=" background-color: red; color: black"
              @click="Delete(item.id)"
            >
              刪除
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
import { useRouter } from "vue-router";
const valueE = ref("");
const isLoading = ref(false);
const store = useCounterStore();
const Toast = useToast();
const page = ref(1);
const totalPage = ref(0);
const pageSize = ref(2);
const currentPlanData = ref([]);
const router = useRouter();
const { proxy } = getCurrentInstance();
const hostName = proxy?.hostname;
const isButton = ref("");
const typePlan = ref("");
const datetimePlan = ref({
  datefrom: "",
  dateto: ""
})

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
    const response = await axios.post(hostName + "/api/Plan/ExportToExcel", datetimePlan.value, {
      headers: {
                    'Accept': 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
                    'Content-Type': 'application/json',
                    'Authorization': `Bearer ${store.getToken}`
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

const NextMap = (id) => {
  router.push({ path: "DetailPlan", query: { id: id, name: "Detail Plan" } });
};
const formatDateTime = (dateTimeString) => {
  const date = new Date(dateTimeString);

  const day = date.getDate().toString().padStart(2, "0");
  const month = (date.getMonth() + 1).toString().padStart(2, "0"); // Tháng bắt đầu từ 0
  const year = date.getFullYear();

  const hours = date.getHours().toString().padStart(2, "0");
  const minutes = date.getMinutes().toString().padStart(2, "0");

  return `${day}/${month}/${year} ${hours}:${minutes}`;
};
const getToken = () => {
  var token = store.getToken;
  var result = {
    headers: { Authorization: `Bearer ${token}` },
  };
  return result;
};
const findAllData = async (search, pageData) => {
  isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
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
              `/api/Plan/FindDoneByAdmin?page=${pageData}&pageSize=${pageSize.value}`,
            getToken()
          )
        : await axios.get(
            hostName +
              `/api/Plan/FindDoneByAdmin?name=${search}&page=${pageData}&pageSize=${pageSize.value}`,
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
  } else if (typePlan.value === "NoReceiver") {
    const res =
      search === ""
        ? await axios.get(
            hostName +
              `/api/Plan/FindNoConfirmationAdmin?page=${pageData}&pageSize=${pageSize.value}`,
            getToken()
          )
        : await axios.get(
            hostName +
              `/api/Plan/FindNoConfirmationAdmin?name=${search}&page=${pageData}&pageSize=${pageSize.value}`,
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
  Toast.success("Success");
  isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
};

const changeReload = (event) => {
  pageSize.value = event;
  findAllData(valueE.value, page.value);
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

const Update = (id) => {
    router.push({path: "/planPage", query: {id: id, name: "Update"}})
}

const Delete = async (id) => {
  if(confirm("Bạn chắn chắn muốn xóa không")){
    const res = await axios.delete(hostName + `/api/Plan/DeleteData?id=${id}`, getToken())
    if(res.data.success){
      Toast.success("Success")
      findAllData(valueE.value, page.value);
    }else{
      Toast.error(res.data.error)
    }
  }
}
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
