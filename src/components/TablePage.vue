<template>
  <div class="table-page">
    <h1>管理</h1>

    <!-- Bảng Kho -->
    <div class="table-container">
      <h2 class="table-title">倉庫管理</h2>
      <router-link to="/AddorEdit" class="waves-effect waves-dark" aria-expanded="false">
        <i class="fa fa-tachometer"></i>
        <span class="hide-menu">添加倉庫</span>
      </router-link>
      <table class="table">
        <thead>
          <tr>
            <th class="title">ID</th>
            <th class="title">圖片</th>
            <th class="title">名稱</th>
            <th class="title">地址</th>
            <th class="title">數量</th>
            <th class="title">數量為空</th>
            <th class="title">帳戶已創建</th>
            <th class="title">#</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(row, rowIndex) in dataWarehourse" :key="rowIndex">
            <td>{{ row.code }}</td>
            <td><img :src="row.image" style="width: 50px; height: 50px; border-radius: 50%;" alt=""></td>
            <td>{{ row.name }}</td>
            <td>{{ row.city }}, {{ row.street }}, {{ row.district }}</td>
            <td>{{ row.numberoffloors }}</td>
            <td>{{ row.numberoffloorsEmty }}</td>
            <td>
              <div style="display: flex;">
                <img :src="row.account_image" style="width: 50px; height: 50px; border-radius: 50%;" alt="">
                <h3 style="margin: 0 15px; font-weight: bold;">{{ row.account_name }}</h3>
              </div>
            </td>
            <td>
              <div style="display: flex;">
                <button class="btn btn-sucess" style="background-color: yellow; font-weight: bold;" @click="NextWarehourse(row.id)">Edit</button>
                <button class="btn btn-sucess" style="background-color: red; color: white; font-weight: bold;">Delete</button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
      <PagesTotal :page="page" :totalPage="totalPage" :valueE="valueE" @pageChange="findAllWarehourse" @pageSizeChange="changeReload"></PagesTotal>
    </div>

    <!-- Bảng Tầng -->
    <div class="table-container">
      <h2 class="table-title"> 樓層管理</h2>
      <router-link to="/AddDataFloorPage" class="waves-effect waves-dark" aria-expanded="false">
        <i class="fa fa-tachometer"></i>
        <span class="hide-menu">新增樓層</span>
      </router-link>
      <table class="table">
        <thead>
          <tr>
            <th class="title">圖片</th>
            <th class="title">名稱</th>
            <th class="title">倉庫</th>
            <th class="title">數量</th>
            <th class="title">數量為空</th>
            <th class="title">帳戶已創建</th>
            <th class="title">#</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(row, rowIndex) in dataFloor" :key="rowIndex">
            <td><img :src="row.image" style="width: 50px; height: 50px; border-radius: 50%;" alt=""></td>
            <td>{{ row.name }}</td>
            <td>
              <div style="display: flex;">
                <img :src="row.warehouse_image" style="width: 30px; height: 30px; border-radius: 50%;" alt="">
                <h5 style="margin: 0 15px; font-weight: bold;">{{ row.warehouse_name }}</h5>
              </div>
            </td>
            <td>{{ row.quantityarea }}</td>
            <td>{{ row.locationEmty }}</td>
            <td>
              <div style="display: flex;">
                <img :src="row.account_image" style="width: 50px; height: 50px; border-radius: 50%;" alt="">
                <h3 style="margin: 0 15px; font-weight: bold;">{{ row.account_name }}</h3>
              </div>
            </td>
            <td>
              <div style="display: flex;">
                <button class="btn btn-sucess" style="background-color: yellow; font-weight: bold;">Edit</button>
                <button class="btn btn-sucess" style="background-color: red; color: white; font-weight: bold;">Delete</button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
      <PagesTotal :page="pageFloor" :totalPage="totalPageFloor" :valueE="valueEFloor" @pageChange="findAllFloor" @pageSizeChange="changeReloadFloor"></PagesTotal>
    </div>

    <!-- Bảng Khu -->
    <div class="table-container">
      <h2 class="table-title">倉庫管理</h2>
      <router-link to="/AddDataAreasPage" class="waves-effect waves-dark" aria-expanded="false">
        <i class="fa fa-tachometer"></i>
        <span class="hide-menu">新增倉庫</span>
      </router-link>
      <table class="table">
        <thead>
          <tr>
            <th class="title">圖片</th>
            <th class="title">名稱</th>
            <th class="title">樓層</th>
            <th class="title">數量</th>
            <th class="title">帳戶已創建</th>
            <th class="title">#</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(row, rowIndex) in dataArea" :key="rowIndex">
            <td><img :src="row.image" style="width: 50px; height: 50px; border-radius: 50%;" alt=""></td>
            <td>{{ row.name }}</td>
            <td>
              <div style="display: flex;">
                <img :src="row.floorImage" style="width: 30px; height: 30px; border-radius: 50%;" alt="">
                <h5 style="margin: 0 15px; font-weight: bold;">{{ row.floorName }}</h5>
              </div>
            </td>
            <td>{{ row.storage }}</td>
            <td>
              <div style="display: flex;">
                <img :src="row.accountImage" style="width: 50px; height: 50px; border-radius: 50%;" alt="">
                <h3 style="margin: 0 15px; font-weight: bold;">{{ row.accountName }}</h3>
              </div>
            </td>
            <td>
              <div style="display: flex;">
                <button class="btn btn-sucess" style="background-color: yellow; font-weight: bold;" @click="NextAreaUpdate(row.id)">修正</button>
                <button class="btn btn-sucess" style="background-color: red; color: white; font-weight: bold;">刪除</button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
      <PagesTotal :page="pageArea" :totalPage="totalPageArea" :valueE="valueEArea" @pageChange="findAllArea" @pageSizeChange="changeReloadArea"></PagesTotal>
    </div>

    <!-- Bảng Line -->
    <div class="table-container">
      <h2 class="table-title">排管理</h2>
      <router-link to="/AddLinePage" class="waves-effect waves-dark" aria-expanded="false">
        <i class="fa fa-tachometer"></i>
        <span class="hide-menu"> 新增排</span>
      </router-link>
      <table class="table">
        <thead>
          <tr>
            <th class="title">名稱</th>
            <th class="title">貨架數量</th>
            <th class="title">倉庫名稱</th>
            <th class="title">倉庫圖片</th>
            <th class="title">代碼</th>
            <th class="title">#</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(row, rowIndex) in dataLine" :key="rowIndex">
            <td>{{ row.name }}</td>
            <td>
              <h5 style="margin: 0 15px; font-weight: bold;">{{ row.quantityshelf }}</h5>
            </td>
            <td>{{ row.area_name }}</td>
            <td><img :src="row.area_image" style="width: 50px; height: 50px; border-radius: 50%;" alt=""></td>
            <td>
              <div style="display: flex;">
                <h3 style="margin: 0 15px; font-weight: bold;">{{ row.code }}</h3>
              </div>
            </td>
            <td>
              <div style="display: flex;">
                <!-- <button class="btn btn-sucess" style="background-color: yellow; font-weight: bold;" @click="NextAreaUpdate(row.id)">Edit</button> -->
                <button class="btn btn-sucess" style="background-color: red; color: white; font-weight: bold;">Delete</button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
      <PagesTotal :page="pageLine" :totalPage="totalPageLine" :valueE="valueELine" @pageChange="findAllLine" @pageSizeChange="changeReloadLine"></PagesTotal>
    </div>
    <!-- Bảng Kệ -->
    <div class="table-container">
      <h2 class="table-title">貨架管理</h2>
      <router-link to="/AddOrEditArea" class="waves-effect waves-dark" aria-expanded="false">
        <i class="fa fa-tachometer"></i>
        <span class="hide-menu">新增貨架</span>
      </router-link>
      <table class="table">
        <thead>
          <tr>
            <th class="title">圖片</th>
            <th class="title">名稱</th>
            <th class="title">排</th>
            <th class="title">數量</th>
            <th class="title">數量為空</th>
            <th class="title">帳戶已創建</th>
            <th class="title">#</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(row, rowIndex) in dataShelfs" :key="rowIndex">
            <td><img :src="row.image" style="width: 50px; height: 50px; border-radius: 50%;" alt=""></td>
            <td>{{ row.name }}</td>
            <td>
              <div style="display: flex;">
                <h5 style="margin: 0 15px; font-weight: bold;">{{ row.lineidsdata }}</h5>
              </div>
            </td>
            <td>{{ row.quantity }}</td>
            <td>{{ row.totalLocationExsis }}</td>
            <td>
              <div style="display: flex;">
                <img :src="row.account_image" style="width: 50px; height: 50px; border-radius: 50%;" alt="">
                <h3 style="margin: 0 15px; font-weight: bold;">{{ row.account_name }}</h3>
              </div>
            </td>
            <td>
              <div style="display: flex;">
                <!-- <button class="btn btn-sucess" style="background-color: yellow; font-weight: bold;" @click="NextAreaUpdate(row.id)">Edit</button> -->
                <button class="btn btn-sucess" style="background-color: red; color: white; font-weight: bold;">刪除</button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
      <PagesTotal :page="pageShelf" :totalPage="totalPageShelf" :valueE="valueEShelf" @pageChange="findAllShelfs" @pageSizeChange="changeReloadShelfs"></PagesTotal>
    </div>
  </div>

  <!-- Hiển thị màn hình loading -->
  <div v-if="isLoading" class="loading-overlay">
      <div class="spinner"></div>
      <p>Loading...</p>
    </div>
</template>

<script setup>
import axios from "axios";
import { ref, getCurrentInstance, watch, onMounted } from "vue";
import PagesTotal from "./PageList/PagesTotal.vue";
import { useCounterStore } from "../store";
import {useRouter} from 'vue-router'
onMounted(() => {
  findAllArea(valueEArea.value, pageArea.value)
  findAllWarehourse(valueE.value, page.value)
  findAllFloor(valueEFloor.value, pageFloor.value)
  findAllShelfs(valueEShelf.value, pageShelf.value)
  findAllLine(valueELine.value, pageLine.value)
  
})
const { proxy } = getCurrentInstance();
const hostName = proxy?.hostname;
const page = ref(1);
const totalPage = ref(0);
const pageSize = ref(2);
const valueE = ref("");
const pageFloor = ref(1);
const totalPageFloor = ref(0);
const pageSizeFloor = ref(2);
const valueEFloor = ref("");
const dataFloor = ref([])
const pageArea = ref(1);
const totalPageArea = ref(0);
const pageSizeArea = ref(2);
const valueEArea = ref("");
const dataArea = ref([])
const store = useCounterStore()
const isLoading = ref(false)
const dataWarehourse = ref([])
const dataShelfs = ref([])
const pageShelf = ref(1);
const totalPageShelf = ref(0);
const pageSizeShelf = ref(2);
const valueEShelf = ref("");
const router = useRouter()

const valueELine = ref("");
const dataLine = ref([])
const pageLine = ref(1);
const pageSizeLine = ref(2);
const totalPageLine = ref(0);

watch(page.value, (newPage) => {
  findAllWarehourse(valueE.value, newPage)
  
  })
  watch(pageArea.value, (newPage) => {
    findAllArea(valueEArea.value, newPage)
  })
  watch(pageFloor.value, (newPage) => {
    findAllFloor(valueEFloor.value, newPage)
  
  })

  watch(pageShelf.value, (newPage) => {
    findAllShelfs(valueEShelf.value, newPage)
  
  })

  watch(pageLine.value, (newPage) => {
    findAllLine(valueELine.value, newPage)
  
  })

  const getToken = () => {
        var token = store.getToken
            var result = {
                headers: {Authorization: `Bearer ${token}`}
            }
            return result
      }
const findAllFloor = async (search, pageData) => {
  isLoading.value = true
    document.body.classList.add('loading') // Add Lớp "loading"
    document.body.style.overflow = 'hidden'
    const res = search === '' ? await axios.get(hostName + `/api/Floor/FindAll?page=${pageData}&pageSize=${pageSizeFloor.value}`, getToken()) 
    : await axios.get(hostName + `/api/Floor/FindAll?name=${search}&page=${pageData}&pageSize=${pageSizeFloor.value}`, getToken())

    if (res.data.success) {
      dataFloor.value = res.data.content.data
      pageFloor.value = res.data.content.page;
      totalPageFloor.value = res.data.content.totalPages;
    }
  isLoading.value = false
    document.body.classList.remove('loading')
    document.body.style.overflow = 'auto'
}
const findAllWarehourse = async (search, pageData) => {
  isLoading.value = true
    document.body.classList.add('loading') // Add Lớp "loading"
    document.body.style.overflow = 'hidden'
    const res = search === '' ? await axios.get(hostName + `/api/Warehouse/FindAll?page=${pageData}&pageSize=${pageSize.value}`, getToken()) 
                              : await axios.get(hostName + `/api/Warehouse/FindAll?name=${search}&page=${pageData}&pageSize=${pageSize.value}`, getToken())

    if (res.data.success) {
      dataWarehourse.value = res.data.content.data
      page.value = res.data.content.page;
      totalPage.value = res.data.content.totalPages;
    }
    isLoading.value = false
    document.body.classList.remove('loading')
    document.body.style.overflow = 'auto'
}

const findAllLine = async(search, pageData) => {
  isLoading.value = true
    document.body.classList.add('loading') // Add Lớp "loading"
    document.body.style.overflow = 'hidden'
  console.log(search)
  const res = await axios.get(hostName + `/api/Line/FindAll?page=${pageData}&pageSize=${pageSizeLine.value}`, getToken())
  if(res.data.success){
    dataLine.value = res.data.content.data
    pageLine.value = res.data.content.page;
    totalPageLine.value = res.data.content.totalPages;
  }

  isLoading.value = false
    document.body.classList.remove('loading')
    document.body.style.overflow = 'auto'
}
const changeReload = (event) => {
    pageSize.value = event
    findAllWarehourse(valueE.value, page.value)
  }

  const changeReloadFloor = (event) => {
    pageSizeFloor.value = event
    findAllFloor(valueEFloor.value, pageFloor.value)
  }
  const findAllArea = async (search, pageData) => {
    isLoading.value = true
    document.body.classList.add('loading') // Add Lớp "loading"
    document.body.style.overflow = 'hidden'
    const res = search === '' ? await axios.get(hostName + `/api/Area/FindAll?page=${pageData}&pageSize=${pageSizeArea.value}`, getToken()) 
                              : await axios.get(hostName + `/api/Area/FindAll?name=${search}&page=${pageData}&pageSize=${pageSizeArea.value}`, getToken())
  
    console.log(res)
    if (res.data.success) {
      dataArea.value = res.data.content.data
      pageArea.value = res.data.content.page;
      totalPageArea.value = res.data.content.totalPages;
    }
    isLoading.value = false
    document.body.classList.remove('loading')
    document.body.style.overflow = 'auto'
  }

  const changeReloadShelfs = (event) =>{
    pageSizeShelf.value = event
    findAllShelfs(valueEShelf.value, pageShelf.value)
  }

  const changeReloadLine = (event) =>{
    pageSizeLine.value = event
    findAllLine(valueELine.value, pageLine.value)
  }
  const findAllShelfs = async (searchData, pageData) => {
    isLoading.value = true
    document.body.classList.add('loading') // Add Lớp "loading"
    document.body.style.overflow = 'hidden'
    const res = searchData === '' ? await axios.get(hostName + `/api/Shelf/FindAll?page=${pageData}&pageSize=${pageSizeShelf.value}`, getToken())
                                  : await axios.get(hostName + `/api/Shelf/FindAll?name=${searchData}&page=${pageData}&pageSize=${pageSizeShelf.value}`, getToken())
    console.log(res)
                                  if(res.data.success){
                                    dataShelfs.value = res.data.content.data
                                    pageShelf.value = res.data.content.page;
                                    totalPageShelf.value = res.data.content.totalPages;
                                  }

                                  isLoading.value = false
    document.body.classList.remove('loading')
    document.body.style.overflow = 'auto'
  }
  const changeReloadArea = (event) => {
    pageSizeArea.value = event
    findAllArea(valueEArea.value, pageArea.value)
  }

  const NextWarehourse = (id) => {
    router.push({path: "AddorEdit", query: {id: id, name: "Update"}})
  }

  const NextAreaUpdate = (id) => {
    router.push({path: "/AddOrEditArea", query: {id: id, name: "Update"}})
  }
</script>

<style scoped>
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
