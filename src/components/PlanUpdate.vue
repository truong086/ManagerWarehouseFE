<template>
    <div>
      <h1 style="font-weight: bold; margin: 15px 0;">{{ route.query.id ? "Update Plan ( " + route.query.id + " )" : "Create Plan" }}</h1>
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
            <div style="display: flex; flex-wrap: wrap;" v-if="showlocationold">
              <div v-for="item in 5" :key="item" style="display: flex;">
                <div v-for="itemY in 6" :key="itemY" >
                  <div v-if="dataLocationOld?.productbyShelf?.some(x => x.location.slice(-2) == item + '' + itemY)">
                  <p v-if="dataLocationOld?.findAllPlanDatas?.some(x => (x.locationOld.slice(-2) == item + '' + itemY && x.areaOld == currentWarehouseOld && x.lineOld == currentLineOld && x.shelfOld == currentShelfOld) 
                  || (x.locationNew.slice(-2) == item + '' + itemY && x.areaNew == currentWarehouseOld && x.lineNew == currentLineOld && x.shelfNew == currentShelfOld))" style="position: absolute; font-weight: bold; font-size: 10px; animation: index1 0.5s ease-in-out infinite;">plan : {{ findOnePlan(dataLocationOld.findAllPlanDatas, item + '' + itemY).id }}, type: {{ findOnePlan(dataLocationOld.findAllPlanDatas, item + '' + itemY).locationOld == currentWarehouseOld + currentLineOld + currentShelfOld + item + '' + itemY ? "Old" : "New" }}</p>
                  <button
                @click="OpenFrame(dataLocationOld.productbyShelf, item + '' + itemY, 'bg_old' + item + '' + itemY)"
                
                 :class="['form-select', 'bg_old' + item + '' + itemY, {active: 'bg_old' + item + '' + itemY === BgOld}]" style="width: 130px; background-color: violet;">{{ item }} - {{ itemY }}</button>
                </div>

                <div v-else>
                  <p v-if="dataLocationOld?.findAllPlanDatas?.some(x => (x.locationOld.slice(-2) == item + '' + itemY && x.areaOld == currentWarehouseOld && x.lineOld == currentLineOld && x.shelfOld == currentShelfOld) 
                  || (x.locationNew.slice(-2) == item + '' + itemY && x.areaNew == currentWarehouseOld && x.lineNew == currentLineOld && x.shelfNew == currentShelfOld))" style="position: absolute; font-weight: bold; font-size: 10px; animation: index1 0.5s ease-in-out infinite;">plan : {{ findOnePlan(dataLocationOld.findAllPlanDatas, item + '' + itemY).id }}, type: {{ findOnePlan(dataLocationOld?.findAllPlanDatas, item + '' + itemY).locationOld == currentWarehouseOld + currentLineOld + currentShelfOld + item + '' + itemY ? "Old" : "New" }}</p>
                  <button
                @click="OpenFrame(dataLocationOld?.productbyShelf, item + '' + itemY, 'bg_old' + item + '' + itemY)"
                
                :class="['form-select', 'bg_old' + item + '' + itemY, {active: 'bg_old' + item + '' + itemY === BgOld}]" style="width: 130px;">{{ item }} - {{ itemY }}</button>
                 </div>
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
            <div style="display: flex;  flex-wrap: wrap;" v-if="showDataLocationNew">
              <div v-for="item in 5" :key="item" style="display: flex;">
                <div v-for="itemY in 6" :key="itemY" >
                  <div v-if="dataLocationNew?.productbyShelf?.some(x => x.location.slice(-2) == item + '' + itemY)">
                  <p v-if="dataLocationNew?.findAllPlanDatas?.some(x => (x.locationOld.slice(-2) == item + '' + itemY && x.areaOld == currentWarehouseNew && x.lineOld == currentLineNew && x.shelfOld == currentShelfNew) 
                  || (x.locationNew.slice(-2) == item + '' + itemY && x.areaNew == currentWarehouseNew && x.lineNew == currentLineNew && x.shelfNew == currentShelfNew))" style="position: absolute; font-weight: bold; font-size: 10px; animation: index1 0.5s ease-in-out infinite;">plan : {{ findOnePlan(dataLocationNew.findAllPlanDatas, item + '' + itemY).id }}, type: {{ findOnePlan(dataLocationNew.findAllPlanDatas, item + '' + itemY).locationOld == currentWarehouseNew + currentLineNew + currentShelfNew + item + '' + itemY ? "Old" : "New" }}</p>
                  <button
                @click="OpenFrameNew(dataLocationNew.productbyShelf, item + '' + itemY)"
                
                :class="['form-select', 'bg_new' + item + '' + itemY, { active: 'bg_new' + item + '' + itemY === BgNew }]" style="width: 130px; background-color: violet;">{{ item }} - {{ itemY }}</button>
                </div>
                
                <div v-else>
                  <p v-if="dataLocationNew?.findAllPlanDatas?.some(x => (x.locationOld.slice(-2) == item + '' + itemY && x.areaOld == currentWarehouseNew && x.lineOld == currentLineNew && x.shelfOld == currentShelfNew) 
                  || (x.locationNew.slice(-2) == item + '' + itemY && x.areaNew == currentWarehouseNew && x.lineNew == currentLineNew && x.shelfNew == currentShelfNew))" style="position: absolute; font-weight: bold; font-size: 10px; animation: index1 0.5s ease-in-out infinite;">plan : {{ findOnePlan(dataLocationNew.findAllPlanDatas, item + '' + itemY).id }}, type: {{ findOnePlan(dataLocationNew?.findAllPlanDatas, item + '' + itemY).locationOld == currentWarehouseNew + currentLineNew + currentShelfNew + item + '' + itemY ? "Old" : "New" }}</p>
                  <button
                @click="OpenFrameNew(dataLocationNew?.productbyShelf, item + '' + itemY)"
                
                :class="['form-select', 'bg_new' + item + '' + itemY, { active: 'bg_new' + item + '' + itemY === BgNew }]" style="width: 130px;">{{ item }} - {{ itemY }}</button>
                 </div>
                </div>
              </div>
              
            </div>
          </div>
          <button v-if="!route.query.id" class="btn" style="border: 1px solid greenyellow; margin: 20px 0;" @click="addplan">
                  Add Plan
              </button>

              <button v-else class="btn" style="border: 1px solid greenyellow; margin: 20px 0;" @click="updatePlan">
                  Update Plan
              </button>

              <button class="btn" style="border: 1px solid black; margin: 20px 0;" @click="backData">
                  Back
              </button>
          </div>
        </div>
        </div>
        
        <div>
          
        </div>
        
        
    </div>

    <!-- Frame hiển thị thông tin -->
    <div v-if="frameVisible" class="frame-popup">
        <div
          class="frame-content"
          :style="{ maxWidth: widthDom + 'px', justifyContent: 'flex-start', border: '1px solid black' }"
        >
          <button @click="closeFrame" class="close-btn" style="background-color: red;">關閉按鈕</button>
          <button @click="ClickDataOld(frameData[0].location)" class="close-btn" style="background-color: blueviolet;">選擇</button>
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
                <span class="info-title">數量:</span> {{ item?.quantity }}
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
          :style="{ maxWidth: widthDom + 'px', justifyContent: 'flex-start', border: '1px solid black' }"
        >
          <button @click="closeFrameNew" class="close-btn" style="background-color: red;">關閉按鈕</button>
          <button @click="ClickDataNew(frameDataNew[0].location)" class="close-btn" style="background-color: blueviolet;">選擇</button>
          <div v-if="frameDataNew?.length > 0">
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
                <span class="info-title">數量:</span> {{ item?.quantity }}
              </div>
              <!-- <button @click="closeFrame" v-if="item.id_plan == 0" class="close-btn">Swap</button> -->
            </div>
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
import {useRoute, useRouter} from 'vue-router'
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
  const showlocationold = ref(false)
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
  const showDataLocationNew = ref(false)
  const checkDataLocation = ref("")
  // const currentLocationNew = ref(null)
  
  const dataPlan = ref({
    location_new: "",
    location_old: "",
    areaOld: "",
    lineOld: "",
    shelfOld: "",
    areaNew: "",
    lineNew: "",
    shelfNew: "",
  })

  onMounted(() => {
    dataPlan.value.location_new = ""
    dataPlan.value.location_old = ""
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
    return item.find(x => x.locationNew.slice(-2) == location || x.locationOld.slice(-2) == location)
  }

  // const lastData = (item) => {
  //   return item.slice(-2)
  // }
  const closeFrameNew = () => {
    frameVisibleNew.value = !frameVisibleNew.value
  }

  const backData = () => {
    router.push("/AllPlanUpdatePage")
  }
  const ClickDataOld = async (location) => {
    
    console.log("Data " + location)

    if(checkLocationExsis(location) == dataPlan.value.location_new){
      alert("Data Exsis")
      return
    }
    const res = !route.query.id ? await axios.get(hostname + `/api/Plan/checkPlanLocationAdd?code=${checkLocationExsis(location)}`)
                                : await axios.get(hostname + `/api/Plan/checkPlanLocationUpdate?id=${route.query.id}&code=${checkLocationExsis(location)}`)
                    
    if(!res.data.success){
      alert("The location is selected by another plan !")
      return
    }

    dataPlan.value.location_old = location
    if(BgOld.value !== null)
        document.querySelector('.' + BgOld.value).style.backgroundColor = 'violet'
    
    document.querySelector('.bg_old' + location.slice(-2)).style.backgroundColor = 'yellow'

    BgOld.value = 'bg_old' + location.slice(-2)

    frameVisible.value = !frameVisible.value

    Swal.fire("Success")
  }

  const countCharactersWithoutSpaces = (str) => str.replace(/\s/g, "").length

  const checkLocationExsis = (location) => {
    if(countCharactersWithoutSpaces(location) == 2){
        const chuyendoi = parseInt(currentLineNew.value)
      const chuyendoiShelf = parseInt(currentShelfNew.value)
      if(chuyendoi <= 9 && chuyendoiShelf <= 9 && !/^0\d+/.test(currentLineNew.value) && !/^0\d+/.test(currentShelfNew.value))
          return currentWarehouseNew.value + '0' + currentLineNew.value + '0' + currentShelfNew.value + '' + location
      
      else if(chuyendoi <= 9 && chuyendoiShelf > 9 && !/^0\d+/.test(currentLineNew.value) && !/^0\d+/.test(currentShelfNew.value))
          return currentWarehouseNew.value + '0' + currentLineNew.value + '' + currentShelfNew.value + '' + location
      else if(chuyendoi > 9 && chuyendoiShelf <= 9 && !/^0\d+/.test(currentLineNew.value) && !/^0\d+/.test(currentShelfNew.value))
          return currentWarehouseNew.value + '' + currentLineNew.value + '0' + currentShelfNew.value + '' + location
      else return currentWarehouseNew.value + '' + currentLineNew.value + '' + currentShelfNew.value + '' + location

    }else if(countCharactersWithoutSpaces(location) == 8){
       return location
    }
  }
  const ClickDataNew = async (location) => {
      
    console.log(location)
    
    if(checkLocationExsis(location) == dataPlan.value.location_old){
      alert("Data Exsis")
      return
    }

    const res = !route.query.id ? await axios.get(hostname + `/api/Plan/checkPlanLocationAdd?code=${checkLocationExsis(location)}`)
                                : await axios.get(hostname + `/api/Plan/checkPlanLocationUpdate?id=${route.query.id}&code=${checkLocationExsis(location)}`)
                    
    if(!res.data.success){
      alert("The location is selected by another plan !")
      return
    }

      if(countCharactersWithoutSpaces(location) == 2){
        const chuyendoi = parseInt(currentLineNew.value)
      const chuyendoiShelf = parseInt(currentShelfNew.value)
      if(chuyendoi <= 9 && chuyendoiShelf <= 9 && !/^0\d+/.test(currentLineNew.value) && !/^0\d+/.test(currentShelfNew.value))
          dataPlan.value.location_new = currentWarehouseNew.value + '0' + currentLineNew.value + '0' + currentShelfNew.value + '' + location
      
      else if(chuyendoi <= 9 && chuyendoiShelf > 9 && !/^0\d+/.test(currentLineNew.value) && !/^0\d+/.test(currentShelfNew.value))
          dataPlan.value.location_new = currentWarehouseNew.value + '0' + currentLineNew.value + '' + currentShelfNew.value + '' + location
      else if(chuyendoi > 9 && chuyendoiShelf <= 9 && !/^0\d+/.test(currentLineNew.value) && !/^0\d+/.test(currentShelfNew.value))
          dataPlan.value.location_new = currentWarehouseNew.value + '' + currentLineNew.value + '0' + currentShelfNew.value + '' + location
      else dataPlan.value.location_new = currentWarehouseNew.value + '' + currentLineNew.value + '' + currentShelfNew.value + '' + location

    }else if(countCharactersWithoutSpaces(location) == 8){
      dataPlan.value.location_new = location
    }
    
    
    
    if(dataLocationNew?.value.productbyShelf.some(x => x.location == checkDataLocation.value)){
      if(BgNew.value != null)
        document.querySelector('.' + BgNew.value).style.backgroundColor = 'violet'
    }else{
      if(BgNew.value != null)
        document.querySelector('.' + BgNew.value).style.backgroundColor = 'white'
    }
      document.querySelector('.bg_new' + location.slice(-2)).style.backgroundColor = 'yellow'

      if(countCharactersWithoutSpaces(location) == 2){
        checkDataLocation.value = currentWarehouseNew.value + '' + currentLineNew.value + '' + currentShelfNew.value + '' + location
      }else if(countCharactersWithoutSpaces(location) == 8){
        checkDataLocation.value = location
      }
      BgNew.value = 'bg_new' + location.slice(-2)

    frameVisibleNew.value = !frameVisibleNew.value

    Swal.fire("Success")
  }
  const OpenFrame = (list, location) => {
    frameData.value = []

    if(list?.length <= 0 || list === null || list === undefined)
      return

    const checkList = list.filter((l) => l.location.slice(-2) == location)
    if(checkList.length <= 0)
      return

    checkList.forEach(element => {
      frameData.value.push(element)
    });

    frameVisible.value = !frameVisible.value
  }
  
  const OpenFrameNew = (list, location) => {
    frameDataNew.value = []
    frameVisibleNew.value = !frameVisibleNew.value
    if(list?.length >= 0 && list !== null && list !== undefined){
      const checkList = list.filter((l) => l.location.slice(-2) == location)
      if(checkList.length <= 0){
        
        frameDataNew.value.push({location: currentWarehouseNew.value + '' + currentLineNew.value + '' + currentShelfNew.value + '' + location})
        return
      }
        

      checkList.forEach(element => {
        frameDataNew.value.push(element)
      });
    } else{
      frameDataNew.value.push({location: currentWarehouseNew.value + '' + currentLineNew.value + '' + currentShelfNew.value + '' + location})
    }

    console.log(frameDataNew.value)
  }

  const closeFrame = () => {
    frameVisible.value = !frameVisible.value
  }
  const selectLine = () => {
    dataPlan.value.location_old = ""
    showlocationold.value = false
    BgOld.value = null
    dataShelfOld.value = []
    dataLineOld.value = []
    dataLocationOld.value = []

    currentLineOld.value = null
    currentShelfOld.value = null
    findAllLineOld()
  }

  const selectShelf = () => {
    dataPlan.value.location_old = ""
    showlocationold.value = false
    BgOld.value = null
    dataShelfOld.value = []
    dataLocationOld.value = []
    
    currentShelfOld.value = null

    findAllShelfOld()
  }

  const selectLocation = () => {
    dataPlan.value.location_old = ""
    showlocationold.value = false
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

    // Tạo mảng chuẩn từ 01 đến 10
    const fullRange = Array.from({ length: 10 }, (_, i) => (i + 1).toString().padStart(2, "0"));
    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + `/api/location_addr/FindAllDataLine?area=${currentWarehouseOld.value}`)
    if(res.data.success){
      Swal.fire("Success")
      dataLineOld.value = res.data.content

      dataLineOld.value = [...dataLineOld.value].sort((a, b) => parseInt(a) - parseInt(b))

      // Tìm các số bị thiếu
      const missingNumbers = fullRange.filter(num => !dataLineOld.value.includes(num));

      dataLineOld.value = [...dataLineOld.value, ...missingNumbers].sort((a, b) => a - b);
    }

    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
  }

  const findAllShelfOld = async () => {
    // Tạo mảng chuẩn từ 01 đến 10
    const fullRange = Array.from({ length: 20 }, (_, i) => (i + 1).toString().padStart(2, "0"));
    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + `/api/location_addr/FindAllDataShelfOne?line=${currentLineOld.value}&area=${currentWarehouseOld.value}`)
    if(res.data.success){
      if(res.data.content.length > 0){
        Swal.fire("Success")
      dataShelfOld.value = res.data.content

      dataShelfOld.value = [...dataShelfOld.value].sort((a, b) => parseInt(a.shelf) - parseInt(b.shelf))

      // 2️⃣ Lấy danh sách số đã có trong content
      const existingShelves = dataShelfOld.value.map(item => item.shelf);

      // 3️⃣ Tìm các số bị thiếu
      const missingNumbers = fullRange.filter(num => !existingShelves.includes(num));

      // 4️⃣ Tạo danh sách các số bị thiếu dưới dạng { shelf: "xx" }
      const missingItems = missingNumbers.map(num => ({ shelf: num }));

      dataShelfOld.value = [...dataShelfOld.value, ...missingItems].sort((a, b) => a.shelf - b.shelf);
      }else{
        for(let i = 1; i <= 20; i++){
          dataShelfOld.value.push({shelf: '' + i})
        }
        
      }
      
    }else{
      dataShelfOld.value = []
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

      // dataLocationOld.value.findAllPlanDatas.map((item) => {
      //   return {
      //     ...item,
      //     locationOld: item.locationOld ? item.locationOld.slice(-2) : '',
      //     locationNew: item.locationNew ? item.locationNew.slice(-2) : ''
      //   }
      // })
    }

    showlocationold.value = true

    console.log(res)
    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
  }


  const selectLineNew = () => {
    dataPlan.value.location_new = ""
    showDataLocationNew.value = false
    BgNew.value = null
    dataShelfNew.value = []
    dataLineNew.value = []
    dataLocationNew.value = []

    currentLineNew.value = null
    currentShelfNew.value = null

    findAllLineNew()
  }

  const selectShelfNew = () => {
    dataPlan.value.location_new = ""
    showDataLocationNew.value = false
    BgNew.value = null
    dataShelfNew.value = []
    dataLocationNew.value = []

    currentShelfNew.value = null
    findAllShelfNew()
  }

  const selectLocationNew = () => {
    dataPlan.value.location_new = ""
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

    const fullRange = Array.from({ length: 10 }, (_, i) => (i + 1).toString().padStart(2, "0"));
    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + `/api/location_addr/FindAllDataLine?area=${currentWarehouseNew.value}`)
    if(res.data.success){
      Swal.fire("Success")
      dataLineNew.value = res.data.content
      dataLineNew.value = [...dataLineNew.value].sort((a, b) => parseInt(a) - parseInt(b))

      // Tìm các số bị thiếu
      const missingNumbers = fullRange.filter(num => !dataLineNew.value.includes(num));

      dataLineNew.value = [...dataLineNew.value, ...missingNumbers].sort((a, b) => a - b);
    }

    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
  }

  const findAllShelfNew = async () => {

    const fullRange = Array.from({ length: 20 }, (_, i) => (i + 1).toString().padStart(2, "0"));
    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + `/api/location_addr/FindAllDataShelfOne?line=${currentLineNew.value}&area=${currentWarehouseNew.value}`)
    if(res.data.success){

      if(res.data.content.length > 0){
        Swal.fire("Success")
      dataShelfNew.value = res.data.content
      dataShelfNew.value = [...dataShelfNew.value].sort((a, b) => parseInt(a.shelf) - parseInt(b.shelf))

      // 2️⃣ Lấy danh sách số đã có trong content
      const existingShelves = dataShelfNew.value.map(item => item.shelf);

      // 3️⃣ Tìm các số bị thiếu
      const missingNumbers = fullRange.filter(num => !existingShelves.includes(num));

      // 4️⃣ Tạo danh sách các số bị thiếu dưới dạng { shelf: "xx" }
      const missingItems = missingNumbers.map(num => ({ shelf: num }));

      dataShelfNew.value = [...dataShelfNew.value, ...missingItems].sort((a, b) => a.shelf - b.shelf);
      }else{
        for(let i = 1; i <= 20; i++){
          dataShelfNew.value.push({shelf: '' + i})
        }
      }
      
    }else{
      dataShelfNew.value = []
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

    showDataLocationNew.value = true

    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
  }
   const updatePlan = async () => {
    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";

  if(dataPlan.value.location_new === dataPlan.value.location_old){
      alert("Data Exsis")
      return
    }
  dataPlan.value.areaNew = currentWarehouseNew.value
  dataPlan.value.lineNew = currentLineNew.value
  dataPlan.value.shelfNew = currentShelfNew.value
  dataPlan.value.areaOld = currentWarehouseOld.value
  dataPlan.value.lineOld = currentLineOld.value
  dataPlan.value.shelfOld = currentShelfOld.value
    const res = await axios.put(hostname + `/api/Plan/UpdateData?id=${route.query.id}`, dataPlan.value)
    if(res.data.success){
      Toast.success("Success")
      router.push("/AllPlanUpdatePage")
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

    BgNew.value = 'bg_new' + dataPlan.value.location_new.slice(-2)
    BgOld.value = 'bg_old' + dataPlan.value.location_old.slice(-2)

    console.log(dataPlan.value)
    console.log(BgNew.value)
    console.log(BgOld.value)


    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
  }

  const addplan = async() => { 
    if(dataPlan.value.location_new === "" || dataPlan.value.location_old === ""){
      alert("No Location !!!")
      return
    }

    if(dataPlan.value.location_new === dataPlan.value.location_old){
      alert("Data Exsis")
      return
    }
        
    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";

  dataPlan.value.areaNew = currentWarehouseNew.value
  dataPlan.value.lineNew = currentLineNew.value
  dataPlan.value.shelfNew = currentShelfNew.value
  dataPlan.value.areaOld = currentWarehouseOld.value
  dataPlan.value.lineOld = currentLineOld.value
  dataPlan.value.shelfOld = currentShelfOld.value

  console.log(dataPlan.value)
    const res = await axios.post(hostname + '/api/Plan/Add', dataPlan.value)
    if(res.data.success){
        console.log(res)
        Toast.success("Success")
        alert("Successs")
        router.push("/AllPlanUpdatePage")
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

<style>

.active {
  border: 3px solid red; /* Hoặc bất kỳ màu nào bạn muốn */
  background-color: yellow !important; /* Hoặc màu nền theo yêu cầu */
}
</style>