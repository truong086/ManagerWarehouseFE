<template>
    <div>
        <input type="text" style="padding: 5px 10px; border-radius: 10px;" placeholder="Location Old" v-model="dataPlan.location_old">
        <input type="text" style="padding: 5px 10px; border-radius: 10px; margin: 0 10px;" placeholder="Location New" v-model="dataPlan.location_new">
        <button class="btn" style="border: 1px solid greenyellow;" @click="addplan">
            Add Plan
        </button>
    </div>

    <div v-if="isLoading" class="loading-overlay">
      <div class="spinner"></div>
      <p>Đang tải...</p>
    </div>
</template>

<script setup>
import axios from 'axios';
import {ref, getCurrentInstance} from 'vue';
import {useRouter} from 'vue-router'
  import {useToast} from 'vue-toastification'

  const router = useRouter()
  const {proxy} = getCurrentInstance()
  const hostname = proxy?.hostname
  const Toast = useToast()
  const isLoading = ref(false)

  const dataPlan = ref({
    location_new: "",
    location_old: ""
  })
  const addplan = async() => { 
    isLoading.value = true;
  document.body.classList.add("loading"); // Add Lớp "loading"
  document.body.style.overflow = "hidden";
    const res = await axios.post(hostname + '/api/Plan/Add', dataPlan.value)
    if(res.data.success){
        console.log(res)
        Toast.success("Success")
        router.push("/SearechProductUpdatePage")
    }else{
        Toast.error(res.data.error)
    }

    isLoading.value = false;
  document.body.classList.remove("loading");
  document.body.style.overflow = "auto";
  }
</script>