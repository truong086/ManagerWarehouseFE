<template>
    <div>
        <input type="text" style="padding: 5px 10px; border-radius: 10px;" placeholder="Location Old" v-model="dataPlan.location_old">
        <input type="text" style="padding: 5px 10px; border-radius: 10px; margin: 0 10px;" placeholder="Location New" v-model="dataPlan.location_new">
        <button v-if="!route.query.id" class="btn" style="border: 1px solid greenyellow;" @click="addplan">
            Add Plan
        </button>

        <button v-else class="btn" style="border: 1px solid greenyellow;" @click="updatePlan">
            Update Plan
        </button>
    </div>

    <div v-if="isLoading" class="loading-overlay">
      <div class="spinner"></div>
      <p>Đang tải...</p>
    </div>
</template>

<script setup>
import axios from 'axios';
import {ref, getCurrentInstance, onMounted} from 'vue';
import {useRouter, useRoute} from 'vue-router'
  import {useToast} from 'vue-toastification'

  const router = useRouter()
  const {proxy} = getCurrentInstance()
  const hostname = proxy?.hostname
  const Toast = useToast()
  const isLoading = ref(false)
  const route = useRoute()

  const dataPlan = ref({
    location_new: "",
    location_old: ""
  })

  onMounted(() => {
    if(route.query.id){
      findOneData(route.query.id)
    }
  })
  
   const updatePlan = async () => {
    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
    const res = await axios.put(hostname + `/api/Plan/UpdateData?id=${route.query.id}`, dataPlan.value)
    if(res.data.success){
      Toast.success("Success")
      router.push("/SearechProductUpdatePage")
      alert("Successs")
    }
    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
   }
  const findOneData = async(id) => {
    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
    const res = await axios.get(hostname + `/api/Plan/FindOne?id=${id}`)
    if(res.data.success){
      dataPlan.value.location_new = res.data.content.locationNew
      dataPlan.value.location_old = res.data.content.locationOld
      console.log(res)
    }

    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
  }

  const addplan = async() => { 
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
    }

    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
  }
</script>