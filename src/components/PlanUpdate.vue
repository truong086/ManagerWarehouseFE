<template>
    <div>
      <h1 style="font-weight: bold; margin: 15px 0;">{{ route.query.id ? "更新計劃 ( " + route.query.id + " )" : "新增計劃" }}</h1>
        <div style="display: flex;">
          <div style="display: flex; ">
          <div :style="{width: dataWidthPlanOld + 'px;'}">
            <div style="margin-top: 50px;">
              <input type="text" ref="inputRef" v-model="inputDataOld" @input="loadDataOldSearch" placeholder="Location" style="padding: 10px 5px; border: 1px dashed black; border-radius: 10px;">
            </div>
            <div style="display: flex; justify-content: center; margin-top: 30px; margin-bottom: 20px; background-color: rgba(169,169,169, 0.1); border-radius: 20px; height: 80px; padding-top: 30px;">
              <div style="display: flex;">
            <h2 style="margin: 0 10px;">舊區域: </h2>
            <select v-model="currentWarehouseOld" @change="selectLine" style="width: 100px; height: 35px;">
              <option v-for="(item, index) in dataAreaOld" :key="index" :value="item">{{ item }}</option>
            </select>
          </div>

          <div style="display: flex; margin: 0 20px;">
            <h2 style="margin: 0 10px;">舊排: </h2>
            <select v-model="currentLineOld" @change="selectShelf" style="width: 100px; height: 35px;">
              <option v-for="(item, index) in dataLineOld" :key="index" :value="item">{{ item }}</option>
            </select>
          </div>

          <div style="display: flex;">
            <h2 style="margin: 0 10px;">舊架: </h2>
            <select v-model="currentShelfOld" @change="selectLocation" style="width: 100px; height: 35px;">
              <option v-for="(item, index) in dataShelfOld" :key="index" :value="item.shelf">{{ item.shelf }}</option>
            </select>
          </div>
            </div>

          <div style="width: 600px; margin: 25px 0;">
            <div style="display: flex;  flex-wrap: wrap;" v-if="showlocationold">
              <div v-for="item in 5" :key="item" style="display: flex; width: 600px;">
                <div v-for="itemY in 6" :key="itemY" >
                  <div v-if="dataLocationOld?.productbyShelf?.some(x => x.location.slice(-2) == item + '' + itemY)">
                  <!-- <p v-if="dataLocationOld?.findAllPlanDatas?.some(x => (x.locationOld.slice(-2) == item + '' + itemY && x.areaOld == currentWarehouseOld && x.lineOld == currentLineOld && x.shelfOld == currentShelfOld) 
                  || (x.locationNew.slice(-2) == item + '' + itemY && x.areaNew == currentWarehouseOld && x.lineNew == currentLineOld && x.shelfNew == currentShelfOld))" style="position: absolute; font-weight: bold; font-size: 10px; animation: index1 0.5s ease-in-out infinite;">plan : {{ findOnePlan(dataLocationOld.findAllPlanDatas, item + '' + itemY).id }}, type: {{ findOnePlan(dataLocationOld.findAllPlanDatas, item + '' + itemY).locationOld == currentWarehouseOld + currentLineOld + currentShelfOld + item + '' + itemY ? "Old" : "New" }}</p> -->
                  
                  <p v-if="dataLocationOld?.findAllPlanDatas?.some(x => (x.locationOld.slice(-2) == item + '' + itemY && x.areaOld == currentWarehouseOld && x.lineOld == currentLineOld && x.shelfOld == currentShelfOld) 
                  || (x.locationNew.slice(-2) == item + '' + itemY && x.areaNew == currentWarehouseOld && x.lineNew == currentLineOld && x.shelfNew == currentShelfOld)) 
                  && findOnePlan(dataLocationOld?.findAllPlanDatas, item + '' + itemY, currentWarehouseOld, currentLineOld, currentShelfOld) != null" 
                  style="position: absolute; font-weight: bold; font-size: 10px; animation: index1 0.5s ease-in-out infinite;">
                  {{ 'plan : ' + findOnePlan(dataLocationOld?.findAllPlanDatas, item + '' + itemY, currentWarehouseOld, currentLineOld, currentShelfOld).id}}, type: {{ findOnePlan(dataLocationOld?.findAllPlanDatas, item + '' + itemY, currentWarehouseOld, currentLineOld, currentShelfOld).locationOld == currentWarehouseOld + currentLineOld + currentShelfOld + item + '' + itemY ? "Old" : "New" }}
                </p>
                  <button
                @click="OpenFrame(dataLocationOld.productbyShelf, item + '' + itemY, 'bg_old' + item + '' + itemY)"
                
                :class="['form-select', 'bg_old' + item + '' + itemY, {active: 'bg_old' + item + '' + itemY === BgOld}]" style="width: 100px; background-color: violet;">{{ item }} - {{ itemY }}</button>
                </div>

                <div v-else>
                  <!-- <p v-if="dataLocationOld?.findAllPlanDatas?.some(x => (x.locationOld.slice(-2) == item + '' + itemY && x.areaOld == currentWarehouseOld && x.lineOld == currentLineOld && x.shelfOld == currentShelfOld) 
                  || (x.locationNew.slice(-2) == item + '' + itemY && x.areaNew == currentWarehouseOld && x.lineNew == currentLineOld && x.shelfNew == currentShelfOld))" style="position: absolute; font-weight: bold; font-size: 10px; animation: index1 0.5s ease-in-out infinite;">plan : {{ findOnePlan(dataLocationOld.findAllPlanDatas, item + '' + itemY).id }}, type: {{ findOnePlan(dataLocationOld?.findAllPlanDatas, item + '' + itemY).locationOld == currentWarehouseOld + currentLineOld + currentShelfOld + item + '' + itemY ? "Old" : "New" }}</p> -->
                  
                  <p v-if="dataLocationOld?.findAllPlanDatas?.some(x => (x.locationOld.slice(-2) == item + '' + itemY && x.areaOld == currentWarehouseOld && x.lineOld == currentLineOld && x.shelfOld == currentShelfOld) 
                  || (x.locationNew.slice(-2) == item + '' + itemY && x.areaNew == currentWarehouseOld && x.lineNew == currentLineOld && x.shelfNew == currentShelfOld)) 
                  && findOnePlan(dataLocationOld?.findAllPlanDatas, item + '' + itemY, currentWarehouseOld, currentLineOld, currentShelfOld) != null" 
                  style="position: absolute; font-weight: bold; font-size: 10px; animation: index1 0.5s ease-in-out infinite;">
                  {{ 'plan : ' + findOnePlan(dataLocationOld?.findAllPlanDatas, item + '' + itemY, currentWarehouseOld, currentLineOld, currentShelfOld).id}}, type: {{ findOnePlan(dataLocationOld?.findAllPlanDatas, item + '' + itemY, currentWarehouseOld, currentLineOld, currentShelfOld).locationOld == currentWarehouseOld + currentLineOld + currentShelfOld + item + '' + itemY ? "Old" : "New" }}
                </p>
                  <button
                @click="OpenFrame(dataLocationOld?.productbyShelf, item + '' + itemY, 'bg_old' + item + '' + itemY)"
                
                :class="['form-select', 'bg_old' + item + '' + itemY, {active: 'bg_old' + item + '' + itemY === BgOld}]" style="width: 100px;">{{ item }} - {{ itemY }}</button>
                 </div>
                </div>
              </div>
            </div>
          </div>
          <div :style="{textAlign: dataAlign}">
            <button @click="showPlanNewData" class="btn" style="font-weight: bold; background-color: aqua; padding: 10px 30px; margin: 15px 0;">交換至新儲位 <i class="fa fa-exchange" aria-hidden="true"></i></button>
          </div>
          
        </div>
          </div>

          <div style="width: 50px; align-items: center; padding-top: 150px;" v-if="showPlanNew">
            <!--<p style="font-size: 15px;"> <span>⏪</span> </p>
            <p style="font-size: 15px;"> <span>⏩</span> </p>-->
            <img src="../assets/Swap.png" width="20px" alt="">
          </div>
          <div style=" width: 600px;" v-if="showPlanNew">
            <div style="margin-top: 50px;">
              <input type="text" ref="inputRefNew" v-model="inputDataNew" @input="loadDataOldNew" placeholder="Location" style="padding: 10px 5px; border: 1px dashed black; border-radius: 10px;">
            </div>
            <div style="display: flex; justify-content: center; margin-bottom: 20px; background-color: rgba(169,169,169, 0.1); border-radius: 20px; height: 80px; padding-top: 30px; margin-top: 30px;">
              <div style="display: flex;">
            <h2 style="margin-right: 10px;">新區域</h2>
            <select v-model="currentWarehouseNew" @change="selectLineNew" style="width: 100px; height: 35px;">
              <option v-for="(item, index) in dataAreaNew" :key="index" :value="item">{{ item }}</option>
            </select>
          </div>

          <div style="display: flex; margin: 0 20px;">
            <h2 style="margin: 0 10px;">新排</h2>
            <select v-model="currentLineNew" @change="selectShelfNew" style="width: 100px; height: 35px;">
              <option v-for="(item, index) in dataLineNew" :key="index" :value="item">{{ item }}</option>
            </select>
          </div>

          <div style="display: flex;">
            <h2 style="margin: 0 10px;">新架</h2>
            <select v-model="currentShelfNew" @change="selectLocationNew" style="width: 100px; height: 35px;">
              <option v-for="(item, index) in dataShelfNew" :key="index" :value="item.shelf">{{ item.shelf }}</option>
            </select>
          </div>
            </div>

          <div style="margin: 25px 0;">
            <div style="display: flex;  flex-wrap: wrap;" v-if="showDataLocationNew">
              <div v-for="item in 5" :key="item" style="display: flex;">
                <div v-for="itemY in 6" :key="itemY" >
                  <div v-if="dataLocationNew?.productbyShelf?.some(x => x.location.slice(-2) == item + '' + itemY)">
                  <!-- <p v-if="dataLocationNew?.findAllPlanDatas?.some(x => (x.locationOld.slice(-2) == item + '' + itemY && x.areaOld == currentWarehouseNew && x.lineOld == currentLineNew && x.shelfOld == currentShelfNew) 
                  || (x.locationNew.slice(-2) == item + '' + itemY && x.areaNew == currentWarehouseNew && x.lineNew == currentLineNew && x.shelfNew == currentShelfNew))" style="position: absolute; font-weight: bold; font-size: 10px; animation: index1 0.5s ease-in-out infinite;">plan : {{ findOnePlan(dataLocationNew.findAllPlanDatas, item + '' + itemY).id }}, type: {{ findOnePlan(dataLocationNew.findAllPlanDatas, item + '' + itemY).locationOld == currentWarehouseNew + currentLineNew + currentShelfNew + item + '' + itemY ? "Old" : "New" }}</p> -->
                  
                  <p v-if="dataLocationNew?.findAllPlanDatas?.some(x => (x.locationOld.slice(-2) == item + '' + itemY && x.areaOld == currentWarehouseNew && x.lineOld == currentLineNew && x.shelfOld == currentShelfNew) 
                  || (x.locationNew.slice(-2) == item + '' + itemY && x.areaNew == currentWarehouseNew && x.lineNew == currentLineNew && x.shelfNew == currentShelfNew)) 
                  && findOnePlan(dataLocationNew.findAllPlanDatas, item + '' + itemY, currentWarehouseNew, currentLineNew, currentShelfNew) != null" 
                  style="position: absolute; font-weight: bold; font-size: 10px; animation: index1 0.5s ease-in-out infinite;">
                  {{ "plan :" +  findOnePlan(dataLocationNew.findAllPlanDatas, item + '' + itemY, currentWarehouseNew, currentLineNew, currentShelfNew).id }}, type: {{ findOnePlan(dataLocationNew?.findAllPlanDatas, item + '' + itemY, currentWarehouseNew, currentLineNew, currentShelfNew).locationOld == currentWarehouseNew + currentLineNew + currentShelfNew + item + '' + itemY ? "Old" : "New" }}
                </p>

                  <button
                @click="OpenFrameNew(dataLocationNew.productbyShelf, item + '' + itemY)"
                
                :class="['form-select', 'bg_new' + item + '' + itemY, { active: 'bg_new' + item + '' + itemY === BgNew }]" style="width: 98px; background-color: violet;">{{ item }} - {{ itemY }}</button>
                </div>

                <div v-else>
                  <!-- <p v-if="dataLocationNew?.findAllPlanDatas?.some(x => (x.locationOld.slice(-2) == item + '' + itemY && x.areaOld == currentWarehouseNew && x.lineOld == currentLineNew && x.shelfOld == currentShelfNew) 
                  || (x.locationNew.slice(-2) == item + '' + itemY && x.areaNew == currentWarehouseNew && x.lineNew == currentLineNew && x.shelfNew == currentShelfNew))" style="position: absolute; font-weight: bold; font-size: 10px; animation: index1 0.5s ease-in-out infinite;">plan : {{ findOnePlan(dataLocationNew.findAllPlanDatas, item + '' + itemY).id }}, type: {{ findOnePlan(dataLocationNew?.findAllPlanDatas, item + '' + itemY).locationOld == currentWarehouseNew + currentLineNew + currentShelfNew + item + '' + itemY ? "Old" : "New" }}</p> -->
                  <p v-if="dataLocationNew?.findAllPlanDatas?.some(x => (x.locationOld.slice(-2) == item + '' + itemY && x.areaOld == currentWarehouseNew && x.lineOld == currentLineNew && x.shelfOld == currentShelfNew) 
                  || (x.locationNew.slice(-2) == item + '' + itemY && x.areaNew == currentWarehouseNew && x.lineNew == currentLineNew && x.shelfNew == currentShelfNew)) 
                  && findOnePlan(dataLocationNew.findAllPlanDatas, item + '' + itemY, currentWarehouseNew, currentLineNew, currentShelfNew) != null" 
                  style="position: absolute; font-weight: bold; font-size: 10px; animation: index1 0.5s ease-in-out infinite;">
                  {{ "plan :" +  findOnePlan(dataLocationNew.findAllPlanDatas, item + '' + itemY, currentWarehouseNew, currentLineNew, currentShelfNew).id }}, type: {{ findOnePlan(dataLocationNew?.findAllPlanDatas, item + '' + itemY, currentWarehouseNew, currentLineNew, currentShelfNew).locationOld == currentWarehouseNew + currentLineNew + currentShelfNew + item + '' + itemY ? "Old" : "New" }}
                </p>
                  <button
                @click="OpenFrameNew(dataLocationNew?.productbyShelf, item + '' + itemY)"
                
                :class="['form-select', 'bg_new' + item + '' + itemY, { active: 'bg_new' + item + '' + itemY === BgNew }]" style="width: 98px;">{{ item }} - {{ itemY }}</button>
                 </div>
                </div>
              </div>
              
            </div>
          </div>
          <button v-if="!route.query.id" class="btn" style="border: 1px solid black; background-color: cornflowerblue; color: white; margin: 0 10px;" @click="addplan">
            新增計劃
              </button>

              <button v-else class="btn" style="border: 1px solid black; background-color: cornflowerblue; color: white; margin: 0 10px;" @click="updatePlan">
                更新計劃
              </button>
              <button class="btn" style="border: 1px solid black; margin: 20px 0;" @click="backData">
                返回
                </button>
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
        <button @click="closeFrame" class="close-btn" style="background-color: red;">取消選取</button>
          <button @click="ClickDataOld(frameData[0].location)" class="close-btn" style="background-color: blueviolet;">確認選取</button>

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
        <button @click="closeFrameNew" class="close-btn" style="background-color: red;">取消選取</button>
          <button @click="ClickDataNew(frameDataNew[0].location)" class="close-btn" style="background-color: blueviolet;">確認選取</button>
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
      <p>載入中......</p>
    </div>
</template>

<script setup>
import axios from 'axios';
import {ref, getCurrentInstance, onMounted, onUpdated, watch, nextTick} from 'vue';
import {useRouter, useRoute} from 'vue-router'
  import {useToast} from 'vue-toastification'
  import Swal from "sweetalert2";

  const router = useRouter()
  const {proxy} = getCurrentInstance()
  const hostname = proxy?.hostname
  const Toast = useToast()
  const isLoading = ref(false)
  const route = useRoute()
  const dataWidthPlanOld = ref(1200)

  const widthDom = ref(1500);
  const dataAlign = ref("center")
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
  const inputDataOld = ref('')
  const inputDataNew = ref('')
  const inputRef = ref(null)
  const inputRefNew = ref(null)
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

  const dataInvalive = ref(null)
  onMounted(async () => {
    dataPlan.value.location_new = ""
    dataPlan.value.location_old = ""
    inputRef.value?.focus()
    if(route.query.id){
      findOneData(route.query.id)
    }

    findAllAreaOld()
    findAllAreaNew()

    await nextTick()
    inputRef.value?.focus()

    dataInvalive.value = setInterval(() => {
      if(!inputDataNew.value.trim() || inputDataNew.value.length <= 0){
        currentLineNew.value = ''
          currentWarehouseNew.value = ''
          currentShelfNew.value = ''
          dataLocationNew.value = []
        dataPlan.value.location_new = ""
        showDataLocationNew.value = false
        frameVisibleNew.value = false
        dataShelfNew.value = []
        dataLineNew.value = []
      }

      if(!inputDataOld.value.trim() || inputDataOld.value.length <= 0){
      currentLineOld.value = ''
        currentWarehouseOld.value = ''
        currentShelfOld.value = ''
        dataLocationOld.value = []
      dataPlan.value.location_old = ""
      showlocationold.value = false
      frameVisible.value = false
      dataLineOld.value = []
          dataShelfOld.value = []
    }

    if(inputDataNew.value.length > 8){
      frameVisibleNew.value = false
      currentLineNew.value = ''
        currentWarehouseNew.value = ''
        currentShelfNew.value = ''
        dataLocationNew.value = []
        showDataLocationNew.value = false
        dataPlan.value.location_new = ""
        dataShelfNew.value = []
        dataLineNew.value = []
    }

    if(inputDataOld.value.length > 8){
      showlocationold.value = false
      currentLineOld.value = ''
        currentWarehouseOld.value = ''
        currentShelfOld.value = ''
        dataLocationOld.value = []
      dataPlan.value.location_old = ""
      frameVisible.value = false
      dataLineOld.value = []
          dataShelfOld.value = []

    }
    }, 300)
  })

  onUpdated(() => { // Kiểm tra khi giao diện đã load hết dữ liệu lên
    // loadDataLast()
    console.log("Hello World")
  })

  watch(inputDataOld.value, async () => {
    await nextTick()
    inputRef.value?.focus()
  })

  watch(inputDataNew.value, async () => {
    if(!inputDataNew.value.trim() || inputDataNew.value.length <= 0){
      currentLineNew.value = ''
        currentWarehouseNew.value = ''
        currentShelfNew.value = ''
        dataLocationNew.value = []
      dataPlan.value.location_new = ""
      showDataLocationNew.value = false
      frameVisibleNew.value = false
      dataShelfNew.value = []
      dataLineNew.value = []
      return
    }
    await nextTick()
    inputRefNew.value?.focus()
  })

  const loadDataOldNew = () =>{
    if(!inputDataNew.value.trim() || inputDataNew.value.length <= 0){
      currentLineNew.value = ''
        currentWarehouseNew.value = ''
        currentShelfNew.value = ''
        dataLocationNew.value = []
      dataPlan.value.location_new = ""
      showDataLocationNew.value = false
      frameVisibleNew.value = false
      dataShelfNew.value = []
      dataLineNew.value = []
      return
    }

    if(inputDataNew.value.length == 2){
      const checkDataAreaLoadNew = dataAreaNew.value.find(x => x == inputDataNew.value)
      if(checkDataAreaLoadNew != null){
        currentWarehouseNew.value = inputDataNew.value
        dataPlan.value.location_new = ""
        selectLineNew()
      }else{
        currentLineNew.value = ''
        dataLocationNew.value = []
        frameVisibleNew.value = false
        showDataLocationNew.value = false
      }
      
    }

    else if(inputDataNew.value.length == 4){
      if(dataLineNew.value.length <= 0){
        currentWarehouseNew.value = inputDataNew.value.split('').slice(0,2).join('')
        findAllLineNew()
        currentLineNew.value = inputDataNew.value.split('').slice(2,4).join('')
          currentWarehouseNew.value = inputDataNew.value.split('').slice(0,2).join('')
          selectShelfNew()
          dataPlan.value.location_new = ""
          return
      }
      const checkDataAreaLoadNew = dataAreaNew.value.find(x => x == inputDataNew.value.split('').slice(0,2).join(''))
        const checkDataLine = dataLineNew.value.find(x => x == inputDataNew.value.split('').slice(2, 4).join(''))
        if(checkDataLine != null && checkDataAreaLoadNew != null){
          currentLineNew.value = inputDataNew.value.split('').slice(2,4).join('')
          currentWarehouseNew.value = inputDataNew.value.split('').slice(0,2).join('')
          selectShelfNew()
        }else{
          currentLineNew.value = ''
          dataLocationNew.value = []
          frameVisibleNew.value = false
          showDataLocationNew.value = false
          dataLineNew.value = []
        }
      dataPlan.value.location_new = ""
      
    }

    else if(inputDataNew.value.length == 6){

      if(dataLineNew.value.length <= 0){
        currentWarehouseNew.value = inputDataNew.value.split('').slice(0,2).join('')
        findAllLineNew()
      }

      const checkDataAreaLoadNew = dataAreaNew.value.find(x => x == inputDataNew.value.split('').slice(0,2).join(''))
        const checkDataLine = dataLineNew.value.find(x => x == inputDataNew.value.split('').slice(2,4).join(''))
        const checkDataShelfLoad = dataShelfNew.value.find(x => x.shelf == inputDataNew.value.split('').slice(4,6).join(''))
        if(checkDataShelfLoad != null && checkDataAreaLoadNew != null && checkDataLine != null){
          currentLineNew.value = inputDataNew.value.split('').slice(2,4).join('')
          currentWarehouseNew.value = inputDataNew.value.split('').slice(0,2).join('')
          currentShelfNew.value = inputDataNew.value.split('').slice(4,6).join('')

          selectLocationNew()
        }else{
          currentWarehouseNew.value = ''
          currentLineNew.value = ''
          currentShelfNew.value = ''
          dataLocationNew.value = []
          frameVisibleNew.value = false
          showDataLocationNew.value = false
          dataShelfNew.value = []
          dataLineNew.value = []
        }

      dataPlan.value.location_new = ""
      
    }

    else if(inputDataNew.value.length == 8){

      if(dataLineNew.value.length <= 0){
        currentWarehouseNew.value = inputDataNew.value.split('').slice(0,2).join('')
        findAllLineNew()
        currentLineNew.value = inputDataNew.value.split('').slice(2,4).join('')
        findAllShelfNew()
        currentShelfNew.value = inputDataNew.value.split('').slice(4,6).join('')

        if(dataLocationNew.value.length <= 0){
          dataPlan.value.location_new = ""
          BgNew.value = null
          findAllLocationNew()
      }
        // OpenFrameNew(dataLocationNew.value.productbyShelf, inputDataNew.value.split('').slice(6,8).join(''))

        return
      }
      // dataLocationOld.value = []
      currentWarehouseNew.value = inputDataNew.value.split('').slice(0,2).join('')
        findAllLineNew()
        currentLineNew.value = inputDataNew.value.split('').slice(2,4).join('')
        findAllShelfNew()
        currentShelfNew.value = inputDataNew.value.split('').slice(4,6).join('')

          dataPlan.value.location_new = ""
          BgNew.value = null
          findAllLocationNew()


      const checkDataAreaLoadOld = dataAreaNew.value.find(x => x == inputDataNew.value.split('').slice(0,2).join(''))
        const checkDataLine = dataLineNew.value.find(x => x == inputDataNew.value.split('').slice(2,4).join(''))
        const checkDataShelfLoad = dataShelfNew.value.find(x => x.shelf == inputDataNew.value.split('').slice(4,6).join(''))
      if(!checkDatamaTran(inputDataNew.value.split('').slice(6,8).join('')) || checkDataShelfLoad == null || checkDataAreaLoadOld == null || checkDataLine == null){
        alert("No Location")
        showDataLocationNew.value = false
        frameVisibleNew.value = false
        dataShelfNew.value = []
        dataLineNew.value = []
        return
      }

      if(dataLocationNew.value.length <= 0){
        currentLineNew.value = inputDataNew.value.split('').slice(2,4).join('')
          currentWarehouseNew.value = inputDataNew.value.split('').slice(0,2).join('')
          currentShelfNew.value = inputDataNew.value.split('').slice(4,6).join('')

          dataPlan.value.location_new = ""
        BgNew.value = null
        findAllLocationNew()
      }
      


      // OpenFrameNew(dataLocationNew.value.productbyShelf, inputDataNew.value.split('').slice(6,8).join(''))
      //if(frameData.value.length > 0)
          //ClickDataOld(currentWarehouseOld.value + '' + currentLineOld.value + '' + currentShelfOld.value + '' + inputDataOld.value.split('').slice(6,8).join(''))

    }

    else if(inputDataNew.value.length > 8){
      frameVisibleNew.value = false
      currentLineNew.value = ''
        currentWarehouseNew.value = ''
        currentShelfNew.value = ''
        dataLocationNew.value = []
        showDataLocationNew.value = false
        dataPlan.value.location_new = ""
        dataShelfNew.value = []
        dataLineNew.value = []
    }

    inputRefNew.value?.focus()
  }

  watch(inputDataOld.value, () => {
    if(!inputDataOld.value.trim() || inputDataOld.value.length <= 0){
      currentLineOld.value = ''
        currentWarehouseOld.value = ''
        currentShelfOld.value = ''
        dataLocationOld.value = []
      dataPlan.value.location_old = ""
      showlocationold.value = false
      frameVisible.value = false
      dataLineOld.value = []
          dataShelfOld.value = []
      return
    }
    loadDataOldSearch()
  })
  const loadDataOldSearch = () => {
    if(!inputDataOld.value.trim() || inputDataOld.value.length <= 0){
      currentLineOld.value = ''
        currentWarehouseOld.value = ''
        currentShelfOld.value = ''
        dataLocationOld.value = []
      dataPlan.value.location_old = ""
      showlocationold.value = false
      frameVisible.value = false
      dataLineOld.value = []
          dataShelfOld.value = []
      return
    }

    if(inputDataOld.value.length == 2){
      const checkDataAreaLoadOld = dataAreaOld.value.find(x => x == inputDataOld.value)
      if(checkDataAreaLoadOld != null){
        currentWarehouseOld.value = inputDataOld.value
        dataPlan.value.location_old = ""
        selectLine()
      }else{
        currentLineOld.value = ''
        dataLocationOld.value = []
        showlocationold.value = false
        frameVisible.value = false
      }
      
    }

    else if(inputDataOld.value.length == 4){
      if(dataLineOld.value.length <= 0){
        currentWarehouseOld.value = inputDataOld.value.split('').slice(0,2).join('')
        findAllLineOld()
        currentLineOld.value = inputDataOld.value.split('').slice(2,4).join('')
          currentWarehouseOld.value = inputDataOld.value.split('').slice(0,2).join('')
          selectShelf()
          dataPlan.value.location_old = ""
          return
      }
      const checkDataAreaLoadOld = dataAreaOld.value.find(x => x == inputDataOld.value.split('').slice(0,2).join(''))
        const checkDataLine = dataLineOld.value.find(x => x == inputDataOld.value.split('').slice(2,4).join(''))
        if(checkDataLine != null && checkDataAreaLoadOld != null){
          currentLineOld.value = inputDataOld.value.split('').slice(2,4).join('')
          currentWarehouseOld.value = inputDataOld.value.split('').slice(0,2).join('')
          selectShelf()
        }else{
          currentLineOld.value = ''
          dataLocationOld.value = []
          showlocationold.value = false
          frameVisible.value = false
          dataLineOld.value = []
        }
      dataPlan.value.location_old = ""
    }

    else if(inputDataOld.value.length == 6){
      
      if(dataLineOld.value.length <= 0){
        currentWarehouseOld.value = inputDataOld.value.split('').slice(0,2).join('')
        findAllLineOld()
      }

      // if(dataShelfOld.value.length <= 0 && dataLineOld.value.length > 0){
      //   currentLineOld.value = inputDataOld.value.split('').slice(2,4).join('')
      //   findAllShelfOld()
      // }
      const checkDataAreaLoadOld = dataAreaOld.value.find(x => x == inputDataOld.value.split('').slice(0,2).join(''))
        const checkDataLine = dataLineOld.value.find(x => x == inputDataOld.value.split('').slice(2,4).join(''))
        const checkDataShelfLoad = dataShelfOld.value.find(x => x.shelf == inputDataOld.value.split('').slice(4,6).join(''))

        if(checkDataShelfLoad != null && checkDataAreaLoadOld != null && checkDataLine != null){
          currentLineOld.value = inputDataOld.value.split('').slice(2,4).join('')
          currentWarehouseOld.value = inputDataOld.value.split('').slice(0,2).join('')
          currentShelfOld.value = inputDataOld.value.split('').slice(4,6).join('')

          selectLocation()
        }else{
          currentWarehouseOld.value = ''
          currentLineOld.value = ''
          currentShelfOld.value = ''
          dataLocationOld.value = []
          showlocationold.value = false
          frameVisible.value = false
          dataLineOld.value = []
          dataShelfOld.value = []
        }

      dataPlan.value.location_old = ""
      
    }

    else if(inputDataOld.value.length == 8){
      // if(dataLineOld.value.length <= 0){
      //     findAllLineOld()
      //   }
      if(dataLineOld.value.length <= 0){
        currentWarehouseOld.value = inputDataOld.value.split('').slice(0,2).join('')
        findAllLineOld()
        currentLineOld.value = inputDataOld.value.split('').slice(2,4).join('')
        findAllShelfOld()
        currentShelfOld.value = inputDataOld.value.split('').slice(4,6).join('')

        if(dataLocationOld.value.length <= 0){
          dataPlan.value.location_old = ""
          BgOld.value = null
          findAllLocationOld()
      }
        // OpenFrame(dataLocationOld.value.productbyShelf, inputDataOld.value.split('').slice(6,8).join(''))

        return
      }
      // dataLocationOld.value = []
      currentWarehouseOld.value = inputDataOld.value.split('').slice(0,2).join('')
        findAllLineOld()
        currentLineOld.value = inputDataOld.value.split('').slice(2,4).join('')
        findAllShelfOld()
        currentShelfOld.value = inputDataOld.value.split('').slice(4,6).join('')

          dataPlan.value.location_old = ""
          findAllLocationOld()

      const checkDataAreaLoadOld = dataAreaOld.value.find(x => x == inputDataOld.value.split('').slice(0,2).join(''))
        const checkDataLine = dataLineOld.value.find(x => x == inputDataOld.value.split('').slice(2,4).join(''))
        const checkDataShelfLoad = dataShelfOld.value.find(x => x.shelf == inputDataOld.value.split('').slice(4,6).join(''))

      if(!checkDatamaTran(inputDataOld.value.split('').slice(6,8).join('')) || checkDataShelfLoad == null || checkDataAreaLoadOld == null || checkDataLine == null){
        alert("No Location !!!")
        dataLineOld.value = []
          dataShelfOld.value = []
        return
      }

      if(dataLocationOld.value.length <= 0){
        currentLineOld.value = inputDataOld.value.split('').slice(2,4).join('')
          currentWarehouseOld.value = inputDataOld.value.split('').slice(0,2).join('')
          currentShelfOld.value = inputDataOld.value.split('').slice(4,6).join('')

          dataPlan.value.location_old = ""
          findAllLocationOld()
      }

      // OpenFrame(dataLocationOld.value.productbyShelf, inputDataOld.value.split('').slice(6,8).join(''))
      
    }

    else if(inputDataOld.value.length > 8){
      showlocationold.value = false
      currentLineOld.value = ''
        currentWarehouseOld.value = ''
        currentShelfOld.value = ''
        dataLocationOld.value = []
      dataPlan.value.location_old = ""
      frameVisible.value = false
      dataLineOld.value = []
          dataShelfOld.value = []

    }

    inputRef.value?.focus()
  }

  const checkDatamaTran = (n) => {
    for(let i = 1; i <= 5; i++){
      for(let y = 1; y <= 6; y++){
        if(i + '' + y == n)
          return true
      }
    }

    return false
  }
  const backData = () => {
    router.push("/AllPlanUpdatePage")
  }

  const showPlanNewData = () => {
    showPlanNew.value = !showPlanNew.value
    dataWidthPlanOld.value = 600
    dataAlign.value = 'center'
  }
  const findOnePlan = (item, location, area, line, shelf) => {
    
    return item.find(x => ((x.locationOld.slice(-2) == location && x.areaOld == area 
    && x.lineOld == line && x.shelfOld == shelf))
               || ((x.locationNew.slice(-2) == location && x.areaNew == area 
               && x.lineNew == line && x.shelfNew == shelf)))
    
    // return item.find(x => x.locationOld.slice(-2) == location || x.locationNew.slice(-2) == location)
  }

  // const lastData = (item) => {
  //   return item.slice(-2)
  // }
  const closeFrameNew = () => {
    frameVisibleNew.value = !frameVisibleNew.value
  }
  const ClickDataOld = async (location) => {
    if(checkLocationExsis(location) == dataPlan.value.location_new){
      alert("Data Exsis")
      return
    }

    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";

    const res = !route.query.id ? await axios.get(hostname + `/api/Plan/checkPlanLocationAdd?code=${checkLocationExsis(location)}`)
                                : await axios.get(hostname + `/api/Plan/checkPlanLocationUpdate?id=${route.query.id}&code=${checkLocationExsis(location)}`)
                    
    if(!res.data.success){
      alert("The location is selected by another plan !")

      isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
      return
    }

    dataPlan.value.location_old = location
    if(BgOld.value !== null)
        document.querySelector('.' + BgOld.value).style.backgroundColor = 'violet'
    
    document.querySelector('.bg_old' + location.slice(-2)).style.backgroundColor = 'yellow'

    BgOld.value = 'bg_old' + location.slice(-2)

    frameVisible.value = !frameVisible.value

    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";

  inputDataOld.value = ''
  const chuyendoi = parseInt(currentLineOld.value)
    const chuyendoiShelf = parseInt(currentShelfOld.value)
    if(chuyendoi <= 9 && chuyendoiShelf <= 9 && !/^0\d+/.test(currentLineOld.value) && !/^0\d+/.test(currentShelfOld.value))
        inputDataOld.value = currentWarehouseOld.value + '0' + currentLineOld.value + '0' + currentShelfOld.value + '' + location.slice(-2)
    
    else if(chuyendoi <= 9 && chuyendoiShelf > 9 && !/^0\d+/.test(currentLineOld.value) && !/^0\d+/.test(currentShelfOld.value))
        inputDataOld.value = currentWarehouseOld.value + '0' + currentLineOld.value + '' + currentShelfOld.value + '' + location.slice(-2)
    else if(chuyendoi > 9 && chuyendoiShelf <= 9 && !/^0\d+/.test(currentLineOld.value) && !/^0\d+/.test(currentShelfOld.value))
        inputDataOld.value = currentWarehouseOld.value + '' + currentLineOld.value + '0' + currentShelfOld.value + '' + location.slice(-2)
    else inputDataOld.value = currentWarehouseOld.value + '' + currentLineOld.value + '' + currentShelfOld.value + '' + location.slice(-2)
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

  const checkLocationExsisOld = (location) => {
    if(countCharactersWithoutSpaces(location) == 2){
        const chuyendoi = parseInt(currentLineOld.value)
      const chuyendoiShelf = parseInt(currentShelfOld.value)
      if(chuyendoi <= 9 && chuyendoiShelf <= 9 && !/^0\d+/.test(currentLineOld.value) && !/^0\d+/.test(currentShelfOld.value))
          return currentWarehouseOld.value + '0' + currentLineOld.value + '0' + currentShelfOld.value + '' + location
      
      else if(chuyendoi <= 9 && chuyendoiShelf > 9 && !/^0\d+/.test(currentLineOld.value) && !/^0\d+/.test(currentShelfOld.value))
          return currentWarehouseOld.value + '0' + currentLineOld.value + '' + currentShelfOld.value + '' + location
      else if(chuyendoi > 9 && chuyendoiShelf <= 9 && !/^0\d+/.test(currentLineOld.value) && !/^0\d+/.test(currentShelfOld.value))
          return currentWarehouseOld.value + '' + currentLineOld.value + '0' + currentShelfOld.value + '' + location
      else return currentWarehouseOld.value + '' + currentLineOld.value + '' + currentShelfOld.value + '' + location

    }else if(countCharactersWithoutSpaces(location) == 8){
       return location
    }
  }
  const ClickDataNew = async (location) => {
    
        
    if(checkLocationExsisOld(location) == dataPlan.value.location_old){
      alert("Data Exsis")
      return
    }

    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
    const res = !route.query.id ? await axios.get(hostname + `/api/Plan/checkPlanLocationAdd?code=${checkLocationExsisOld(location)}`)
                                : await axios.get(hostname + `/api/Plan/checkPlanLocationUpdate?id=${route.query.id}&code=${checkLocationExsisOld(location)}`)
                    
    if(!res.data.success){
      alert("The location is selected by another plan !")
      isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
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
    
    console.log(BgNew.value)
    if(dataLocationNew?.value.productbyShelf.some(x => x.location == checkDataLocation.value)){
      if(checkDataLocation.value != "")
        document.querySelector('.bg_new' + checkDataLocation.value.slice(-2)).style.backgroundColor = 'violet'
      if(BgNew.value != null)
        document.querySelector('.' + BgNew.value).style.backgroundColor = 'violet'
    }else{
      if(checkDataLocation.value != "")
        document.querySelector('.bg_new' + checkDataLocation.value.slice(-2)).style.backgroundColor = 'white'
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

    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";

  inputDataNew.value = ''
  const chuyendoi = parseInt(currentLineNew.value)
    const chuyendoiShelf = parseInt(currentShelfNew.value)
    if(chuyendoi <= 9 && chuyendoiShelf <= 9 && !/^0\d+/.test(currentLineNew.value) && !/^0\d+/.test(currentShelfNew.value))
      inputDataNew.value = currentWarehouseNew.value + '0' + currentLineNew.value + '0' + currentShelfNew.value + '' + location.slice(-2)
    
    else if(chuyendoi <= 9 && chuyendoiShelf > 9 && !/^0\d+/.test(currentLineNew.value) && !/^0\d+/.test(currentShelfNew.value))
      inputDataNew.value = currentWarehouseNew.value + '0' + currentLineNew.value + '' + currentShelfNew.value + '' + location.slice(-2)
    else if(chuyendoi > 9 && chuyendoiShelf <= 9 && !/^0\d+/.test(currentLineNew.value) && !/^0\d+/.test(currentShelfNew.value))
      inputDataNew.value = currentWarehouseNew.value + '' + currentLineNew.value + '0' + currentShelfNew.value + '' + location.slice(-2)
    else inputDataNew.value = currentWarehouseNew.value + '' + currentLineNew.value + '' + currentShelfNew.value + '' + location.slice(-2)
  }
 
  const OpenFrameNew = (list, location) => {

    if(!checkDatamaTran(location.slice(-2)))
        return

    if(location == '' || location == null || location == undefined)
        return
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
  }

  const OpenFrame = (list, location) => {
    frameData.value = []

    if(!checkDatamaTran(location.slice(-2)))
        return

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
  

  const closeFrame = () => {
    frameVisible.value = !frameVisible.value
  }
  const selectLine = () => {
    inputDataOld.value = ""
    dataPlan.value.location_old = ""
    showlocationold.value = false
    BgOld.value = null
    dataShelfOld.value = []
    dataLineOld.value = []
    dataLocationOld.value = []

    currentLineOld.value = null
    currentShelfOld.value = null
    findAllLineOld()

    inputDataOld.value = currentWarehouseOld.value
  }

  const selectShelf = () => {
    dataPlan.value.location_old = ""
    showlocationold.value = false
    BgOld.value = null
    dataShelfOld.value = []
    dataLocationOld.value = []
    
    currentShelfOld.value = null

    findAllShelfOld()

    const chuyendoi = parseInt(currentLineOld.value)
    if(chuyendoi <= 9 && !/^0\d+/.test(currentLineOld.value) && !/^0\d+/.test(currentShelfOld.value))
      inputDataOld.value = currentWarehouseOld.value + '0' + currentLineOld.value
    else 
    inputDataOld.value = currentWarehouseOld.value +  '' + currentLineOld.value
  }
  const selectLocation = () => {
    dataPlan.value.location_old = ""
    showlocationold.value = false
    BgOld.value = null
    dataLocationOld.value = []
    findAllLocationOld()

    const chuyendoi = parseInt(currentLineOld.value)
    const chuyendoiShelf = parseInt(currentShelfOld.value)
    if(chuyendoi <= 9 && chuyendoiShelf <= 9 && !/^0\d+/.test(currentLineOld.value) && !/^0\d+/.test(currentShelfOld.value))
        inputDataOld.value = currentWarehouseOld.value + '0' + currentLineOld.value + '0' + currentShelfOld.value
    
    else if(chuyendoi <= 9 && chuyendoiShelf > 9 && !/^0\d+/.test(currentLineOld.value) && !/^0\d+/.test(currentShelfOld.value))
        inputDataOld.value = currentWarehouseOld.value + '0' + currentLineOld.value + '' + currentShelfOld.value
    else if(chuyendoi > 9 && chuyendoiShelf <= 9 && !/^0\d+/.test(currentLineOld.value) && !/^0\d+/.test(currentShelfOld.value))
    inputDataOld.value = currentWarehouseOld.value + '' + currentLineOld.value + '0' + currentShelfOld.value
    else inputDataOld.value = currentWarehouseOld.value + '' + currentLineOld.value + '' + currentShelfOld.value
    
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

  await nextTick()
  inputRef.value?.focus()
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

    OpenFrame(dataLocationOld.value.productbyShelf, inputDataOld.value.split('').slice(6,8).join(''))
    showlocationold.value = true

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

    inputDataNew.value = currentWarehouseNew.value

    findAllLineNew()
  }

  const selectShelfNew = () => {
    inputDataNew.value = ""
    dataPlan.value.location_new = ""
    showDataLocationNew.value = false
    BgNew.value = null
    dataShelfNew.value = []
    dataLocationNew.value = []

    currentShelfNew.value = null
    findAllShelfNew()

    const chuyendoi = parseInt(currentLineNew.value)
    if(chuyendoi <= 9 && !/^0\d+/.test(currentLineNew.value) && !/^0\d+/.test(currentShelfNew.value))
      inputDataNew.value = currentWarehouseNew.value + '0' + currentLineNew.value
    else 
    inputDataNew.value = currentWarehouseNew.value +  '' + currentLineNew.value
  }

  const selectLocationNew = () => {
    inputDataNew.value = ""
    dataPlan.value.location_new = ""
    BgNew.value = null
    dataLocationNew.value = []
    findAllLocationNew()

    const chuyendoi = parseInt(currentLineNew.value)
    const chuyendoiShelf = parseInt(currentShelfNew.value)
    if(chuyendoi <= 9 && chuyendoiShelf <= 9 && !/^0\d+/.test(currentLineNew.value) && !/^0\d+/.test(currentShelfNew.value))
        inputDataNew.value = currentWarehouseNew.value + '0' + currentLineNew.value + '0' + currentShelfNew.value
    
    else if(chuyendoi <= 9 && chuyendoiShelf > 9 && !/^0\d+/.test(currentLineNew.value) && !/^0\d+/.test(currentShelfNew.value))
      inputDataNew.value = currentWarehouseNew.value + '0' + currentLineNew.value + '' + currentShelfNew.value
    else if(chuyendoi > 9 && chuyendoiShelf <= 9 && !/^0\d+/.test(currentLineNew.value) && !/^0\d+/.test(currentShelfNew.value))
        inputDataNew.value = currentWarehouseNew.value + '' + currentLineNew.value + '0' + currentShelfNew.value
    else inputDataNew.value = currentWarehouseNew.value + '' + currentLineNew.value + '' + currentShelfNew.value
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

  await nextTick()
  inputRef.value?.focus()
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

  await nextTick()
  inputRefNew.value?.focus()
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
OpenFrameNew(dataLocationNew.value.productbyShelf, inputDataNew.value.split('').slice(6,8).join(''))
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

      checkDataLocation.value = dataPlan.value.location_new
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
      inputDataOld.value = dataPlan.value.location_old
      inputDataNew.value = dataPlan.value.location_new
    }


    BgNew.value = 'bg_new' + dataPlan.value.location_new.slice(-2)
    BgOld.value = 'bg_old' + dataPlan.value.location_old.slice(-2)


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

    const res = await axios.post(hostname + '/api/Plan/Add', dataPlan.value)
    if(res.data.success){
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

<style>

.active {
  border: 3px solid red; /* Hoặc bất kỳ màu nào bạn muốn */
  background-color: yellow !important; /* Hoặc màu nền theo yêu cầu */
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