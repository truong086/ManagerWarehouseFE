<template>
    <div>
      <h1 style="font-weight: bold; margin: 15px 0;">Create Plan</h1>
        <!-- <input type="text" style="padding: 5px 10px; border-radius: 10px;" placeholder="Location Old" v-model="dataPlan.location_old">
        <input type="text" style="padding: 5px 10px; border-radius: 10px; margin: 0 10px;" placeholder="Location New" v-model="dataPlan.location_new">
        <button v-if="!route.query.id" class="btn" style="border: 1px solid greenyellow;" @click="addplan">
            Add Plan
        </button>

        <button v-else class="btn" style="border: 1px solid greenyellow;" @click="updatePlan">
            Update Plan
        </button> -->
        <div>
          <div style="width: 800px; margin: 0 auto;">
          <div>
            <div>
            <h2 class="title">Area Old</h2>
            <select class="form-select" v-model="currentWarehouseOld" @change="selectLine">
              <option v-for="(item, index) in dataAreaOld" :key="index" :value="item">{{ item }}</option>
            </select>
          </div>

          <div>
            <h2 class="title">Line Old</h2>
            <select class="form-select" v-model="currentLineOld" @change="selectShelf">
              <option v-for="(item, index) in dataLineOld" :key="index" :value="item">{{ item }}</option>
            </select>
          </div>

          <div>
            <h2 class="title">Shelf Old</h2>
            <select class="form-select" v-model="currentShelfOld" @change="selectLocation">
              <option v-for="(item, index) in dataShelfOld" :key="index" :value="item.shelf">{{ item.shelf }}</option>
            </select>
          </div>

          <div>
            <h2 class="title">Location Old</h2>
            <div style="display: flex; flex-wrap: wrap;">
              <div v-for="(item, index) in dataLocationOld.location" :key="index" >
                <div v-if="dataLocationOld.findAllPlanDatas.some(x => x.locationOld == item || x.locationNew == item)">
                  <p style="position: absolute; font-weight: bold; font-size: 10px; animation: index1 0.5s ease-in-out infinite;">plan : {{ findOnePlan(dataLocationOld.findAllPlanDatas, item).id }}, type: {{ findOnePlan(dataLocationOld.findAllPlanDatas, item).locationOld == item ? "Old" : "New" }}</p>
                  <button
                @click="OpenFrame(dataLocationOld.productbyShelf, item)"
                
                 :class="'form-select ' + 'bg_old' + item" style="width: 100px; background-color: violet;">{{ lastData(item) }}</button>
                </div>

                 <div v-else>
                  <button
                @click="OpenFrame(dataLocationOld.productbyShelf, item)"
                
                :class="'form-select ' + 'bg_old' + item" style="width: 100px;">{{ lastData(item) }}</button>
                 </div>
              </div>
            </div>
          </div>
          </div>

          <div style="text-align: left;">
            <button @click="showPlanNewData" class="btn" style="font-weight: bold; background-color: aqua; padding: 10px 30px; margin: 15px 0;">Swap Location New <i class="fa fa-sign-language" aria-hidden="true"></i></button>
          </div>

          <div style="margin-top: 100px;" v-if="showPlanNew">
            <div>
            <h2 class="title">Area New</h2>
            <select class="form-select" v-model="currentWarehouseNew" @change="selectLineNew">
              <option v-for="(item, index) in dataAreaNew" :key="index" :value="item">{{ item }}</option>
            </select>
          </div>

          <div>
            <h2 class="title">Line New</h2>
            <select class="form-select" v-model="currentLineNew" @change="selectShelfNew">
              <option v-for="(item, index) in dataLineNew" :key="index" :value="item">{{ item }}</option>
            </select>
          </div>

          <div>
            <h2 class="title">Shelf New</h2>
            <select class="form-select" v-model="currentShelfNew" @change="selectLocationNew">
              <option v-for="(item, index) in dataShelfNew" :key="index" :value="item.shelf">{{ item.shelf }}</option>
            </select>
          </div>

          <div>
            <h2 class="title">Location New</h2>
            <div style="display: flex;  flex-wrap: wrap;">
              <div v-for="(item, index) in dataLocationNew.location" :key="index" >
                <div v-if="dataLocationNew.findAllPlanDatas.some(x => x.locationOld == item || x.locationNew == item)">
                  <p style="position: absolute; font-weight: bold; font-size: 10px; animation: index1 0.5s ease-in-out infinite;">plan : {{ findOnePlan(dataLocationNew.findAllPlanDatas, item).id }}, type: {{ findOnePlan(dataLocationNew.findAllPlanDatas, item).locationOld == item ? "Old" : "New" }}</p>
                  <button
                    @click="OpenFrameNew(dataLocationNew.productbyShelf, item)"
                    
                    :class="'form-select ' + 'bg_new' + item" style="width: 100px; background-color: violet;">{{ lastData(item) }}</button>
                </div>
                <div v-else>
                  <button
                  @click="OpenFrameNew(dataLocationNew.productbyShelf, item)"
                  
                  :class="'form-select ' + 'bg_new' + item" style="width: 100px;">{{ lastData(item) }}</button>
                </div>
                

                 
              </div>
              
            </div>
          </div>
          </div>
        </div>
        </div>
        
        <div>
          
        </div>
        <button v-if="!route.query.id" class="btn" style="border: 1px solid greenyellow; margin: 20px 0;" @click="addplan">
                  Add Plan
              </button>

              <button v-else class="btn" style="border: 1px solid greenyellow; margin: 20px 0;" @click="updatePlan">
                  Update Plan
              </button>
        
    </div>

    <!-- Frame hiển thị thông tin -->
    <div v-if="frameVisible" class="frame-popup">
        <div
          class="frame-content"
          :style="{ maxWidth: widthDom + 'px', justifyContent: 'flex-start' }"
        >
          <button @click="closeFrame" class="close-btn">關閉按鈕</button>
          <button @click="ClickDataOld(frameData[0].location)" class="close-btn">選擇</button>
          <div
            class="frame-item"
            v-for="(item, index) in frameData"
            :key="index"
            style="margin: 10px 50px"
          >
            <h3>{{ item?.title }}</h3>
            
            <div class="frame-info">
              <div class="info-line">
                <span class="info-title">位置:</span> {{ item?.location }}
                <span class="info-title">位置:</span> {{ item?.quantity }}
              </div>
              <!-- <button @click="closeFrame" v-if="item.id_plan == 0" class="close-btn">Swap</button> -->
            </div>
          </div>
        </div>
      </div>


      <!-- Frame hiển thị thông tin -->
    <div v-if="frameVisibleNew" class="frame-popup">
        <div
          class="frame-content"
          :style="{ maxWidth: widthDom + 'px', justifyContent: 'flex-start' }"
        >
          <button @click="closeFrameNew" class="close-btn">關閉按鈕</button>
          <button @click="ClickDataNew(frameDataNew[0].location)" class="close-btn">選擇</button>
          <div
            class="frame-item"
            v-for="(item, index) in frameDataNew"
            :key="index"
            style="margin: 10px 50px"
          >
            <h3>{{ item?.title }}</h3>
            
            <div class="frame-info">
              <div class="info-line">
                <span class="info-title">位置:</span> {{ item?.location }}
                <span class="info-title">位置:</span> {{ item?.quantity }}
              </div>
              <!-- <button @click="closeFrame" v-if="item.id_plan == 0" class="close-btn">Swap</button> -->
            </div>
          </div>
        </div>
      </div>
    <div v-if="isLoading" class="loading-overlay">
      <div class="spinner"></div>
      <p>Đang tải...</p>
    </div>
</template>

<script setup>
import axios from 'axios';
import {ref, getCurrentInstance, onMounted, onUpdated} from 'vue';
import {useRouter, useRoute} from 'vue-router'
  import {useToast} from 'vue-toastification'
  import Swal from "sweetalert2";

  const router = useRouter()
  const {proxy} = getCurrentInstance()
  const hostname = proxy?.hostname
  const Toast = useToast()
  const isLoading = ref(false)
  const route = useRoute()

  const widthDom = ref(1500);

  const frameVisible = ref(false)
  const frameVisibleNew = ref(false)

  const currentWarehouseOld = ref(null)
  const currentLineOld = ref(null)
  const currentShelfOld = ref(null)
  const dataAreaOld = ref([])
  const dataLineOld = ref([])
  const dataShelfOld = ref([])
  const dataLocationOld = ref([])
  const showPlanNew = ref(false)
  const frameData = ref([])
  const frameDataNew = ref([])
  // const currentLocationOld = ref(null)

  const currentWarehouseNew = ref(null)
  const currentLineNew = ref(null)
  const currentShelfNew = ref(null)

  const BgOld = ref(null)
  const dataAreaNew = ref([])
  const dataLineNew = ref([])
  const dataShelfNew = ref([])
  const dataLocationNew = ref([])
  const BgNew = ref(null)
  // const currentLocationNew = ref(null)

  
  const dataPlan = ref({
    location_new: "",
    location_old: ""
  })

  onMounted(() => {
    if(route.query.id){
      findOneData(route.query.id)
    }

    findAllAreaOld()
    findAllAreaNew()
  })

  onUpdated(() => { // Kiểm tra khi giao diện đã load hết dữ liệu lên
    // loadDataLast()
    console.log("Hello World")
  })
  const showPlanNewData = () => {
    showPlanNew.value = !showPlanNew.value
  }
  const findOnePlan = (item, location) => {
    return item.find(x => x.locationNew == location || x.locationOld == location)
  }

  const lastData = (item) => {
    return item.slice(-2)
  }
  const closeFrameNew = () => {
    frameVisibleNew.value = !frameVisibleNew.value
  }
  const ClickDataOld = (location) => {
    dataPlan.value.location_old = location
    console.log("Data " + location)
    if(BgOld.value !== null)
        document.querySelector('.' + BgOld.value).style.backgroundColor = 'white'
    
    document.querySelector('.bg_old' + location).style.backgroundColor = 'yellow'

    BgOld.value = 'bg_old' + location

    frameVisible.value = !frameVisible.value

    Swal.fire("Success")
  }

  const ClickDataNew = (location) => {
    dataPlan.value.location_new = location
    if(BgNew.value != null)
        document.querySelector('.' + BgNew.value).style.backgroundColor = 'white'
    
    document.querySelector('.bg_new' + location).style.backgroundColor = 'yellow'

    BgNew.value = 'bg_new' + location

    frameVisibleNew.value = !frameVisibleNew.value

    Swal.fire("Success")
  }
  const OpenFrame = (list, location) => {
    frameData.value = []
    console.log(list)
    console.log(location)

    if(list.length <= 0)
      return

    const checkList = list.filter((l) => l.location == location)
    if(checkList.length <= 0)
      return

    checkList.forEach(element => {
      frameData.value.push(element)
    });

    frameVisible.value = !frameVisible.value
  }
  
  const OpenFrameNew = (list, location) => {
    frameDataNew.value = []

    if(list.length <= 0)
      return

    const checkList = list.filter((l) => l.location == location)
    if(checkList.length <= 0)
      return

    checkList.forEach(element => {
      frameDataNew.value.push(element)
    });

    frameVisibleNew.value = !frameVisibleNew.value
  }

  const closeFrame = () => {
    frameVisible.value = !frameVisible.value
  }
  const selectLine = () => {
    BgOld.value = null
    dataShelfOld.value = []
    dataLineOld.value = []
    dataLocationOld.value = []

    currentLineOld.value = null
    currentShelfOld.value = null
    findAllLineOld()
  }

  const selectShelf = () => {
    BgOld.value = null
    dataShelfOld.value = []
    dataLocationOld.value = []
    
    currentShelfOld.value = null

    findAllShelfOld()
  }

  const selectLocation = () => {
    BgOld.value = null
    dataLocationOld.value = []
    findAllLocationOld()
  }

  const findAllAreaOld = async () => {
    BgOld.value = null
    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + '/api/location_addr/FindAllData')
    if(res.data.success){
      Swal.fire("Success")
      dataAreaOld.value = res.data.content.sort((a, b) => {
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

  const findAllLineOld = async () => {

    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + `/api/location_addr/FindAllDataLine?area=${currentWarehouseOld.value}`)
    if(res.data.success){
      Swal.fire("Success")
      dataLineOld.value = res.data.content

      dataLineOld.value = [...dataLineOld.value].sort((a, b) => parseInt(a) - parseInt(b))
    }

    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
  }

  const findAllShelfOld = async () => {
    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + `/api/location_addr/FindAllDataShelfOne?line=${currentLineOld.value}&area=${currentWarehouseOld.value}`)
    if(res.data.success){
      Swal.fire("Success")
      dataShelfOld.value = res.data.content

      dataShelfOld.value = [...dataShelfOld.value].sort((a, b) => parseInt(a.shelf) - parseInt(b.shelf))
    }

    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
  }

  const findAllLocationOld = async () => {
    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + `/api/location_addr/FindAllDataLocation?line=${currentLineOld.value}&area=${currentWarehouseOld.value}&shelf=${currentShelfOld.value}`)
    if(res.data.success){
      Swal.fire("Success")
      dataLocationOld.value = res.data.content

      dataLocationOld.value.location = [...dataLocationOld.value.location].sort((a, b) => {
        const numA = parseInt(a.slice(-2))
        const numB = parseInt(b.slice(-2))

        return numA - numB
      }) 

    }

    console.log(res)
    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
  }


  const selectLineNew = () => {
    BgNew.value = null
    dataShelfNew.value = []
    dataLineNew.value = []
    dataLocationNew.value = []

    currentLineNew.value = null
    currentShelfNew.value = null

    findAllLineNew()
  }

  const selectShelfNew = () => {
    BgNew.value = null
    dataShelfNew.value = []
    dataLocationNew.value = []

    currentShelfNew.value = null
    findAllShelfNew()
  }

  const selectLocationNew = () => {
    BgNew.value = null
    dataLocationNew.value = []
    findAllLocationNew()
  }

  const findAllAreaNew = async () => {
    BgOld.value = null
    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + '/api/location_addr/FindAllData')
    if(res.data.success){
      Swal.fire("Success")
      dataAreaNew.value = res.data.content.sort((a, b) => {
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

  const findAllLineNew = async () => {

    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + `/api/location_addr/FindAllDataLine?area=${currentWarehouseNew.value}`)
    if(res.data.success){
      Swal.fire("Success")
      dataLineNew.value = res.data.content
      dataLineNew.value = [...dataLineNew.value].sort((a, b) => parseInt(a) - parseInt(b))
    }

    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
  }

  const findAllShelfNew = async () => {
    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + `/api/location_addr/FindAllDataShelfOne?line=${currentLineNew.value}&area=${currentWarehouseNew.value}`)
    if(res.data.success){
      Swal.fire("Success")
      dataShelfNew.value = res.data.content
      dataShelfNew.value = [...dataShelfNew.value].sort((a, b) => parseInt(a.shelf) - parseInt(b.shelf))
    }

    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
  }

  // const loadDataFirst = () => {
  //   isLoading.value = true;
  // document.body.classList.add("loading"); // Add Lớp "loading"
  // document.body.style.overflow = "hidden";
  // }
  const loadDataLast = () =>{
    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
  }

  const findAllLocationNew = async () => {
    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + `/api/location_addr/FindAllDataLocation?line=${currentLineNew.value}&area=${currentWarehouseNew.value}&shelf=${currentShelfNew.value}`)
    if(res.data.success){
      Swal.fire("Success")
      dataLocationNew.value = res.data.content

      dataLocationNew.value.location = [...dataLocationNew.value.location].sort((a, b) => {
        const numA = parseInt(a.slice(-2))
        const numB = parseInt(b.slice(-2))

        return numA - numB
      }) 
    }

    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
  }
   const updatePlan = async () => {
    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
    const res = await axios.put(hostname + `/api/Plan/UpdateData?id=${route.query.id}`, dataPlan.value)
    if(res.data.success){
      Toast.success("Success")
      router.push("/SearechProductUpdatePage")
      alert("Successs")
    }else{
      alert("error")
    }
    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
   }
  const findOneData = async(id) => {
    
    /**
     * 
     *   const currentWarehouseNew = ref(null)
  const currentLineNew = ref(null)
  const currentShelfNew = ref(null)

    const currentWarehouseOld = ref(null)
  const currentLineOld = ref(null)
  const currentShelfOld = ref(null)
     */
    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + `/api/Plan/FindOne?id=${id}`)
    if(res.data.success){
      dataPlan.value.location_new = res.data.content.locationNew
      dataPlan.value.location_old = res.data.content.locationOld

      currentWarehouseOld.value = res.data.content.areaOld
      currentLineOld.value = res.data.content.lineOld
      currentShelfOld.value = res.data.content.shelfOld
      currentWarehouseNew.value = res.data.content.areaNew
      currentLineNew.value = res.data.content.lineNew
      currentShelfNew.value = res.data.content.shelfNew

      findAllAreaOld()
      findAllLineOld()
      findAllShelfOld()
      findAllLocationOld()

      findAllAreaNew()
      findAllLineNew()
      findAllShelfNew()
      findAllLocationNew()
      
      loadDataLast()
      
      // document.querySelector('.' + BgNew.value).style.backgroundColor = 'violet'
    
      showPlanNew.value = !showPlanNew.value
    }

    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
  }

  const addplan = async() => { 
    if(dataPlan.value.location_new === "" || dataPlan.value.location_old === ""){
      alert("No Location !!!")
      return
    }
        
    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
    const res = await axios.post(hostname + '/api/Plan/Add', dataPlan.value)
    if(res.data.success){
        console.log(res)
        Toast.success("Success")
        alert("Successs")
        router.push("/SearechProductUpdatePage")
    }else{
        Toast.error(res.data.error)
        alert(res.data.error)
    }

    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
  }
</script>

<style>
@keyframes index1 {
  0% {
    color: red;
  }
  100% {
    color: yellow;
  }
}
</style>
<style scope>

.form-select,
.form-input,
.form-textarea {
  height: 50px;
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.form-select {
  cursor: pointer;
}
</style>