<template>
  <aside :class="{'left-sidebar': true, 'collapsed': !isSidebarOpen}">
      <!-- Nút menu để ẩn/hiện sidebar -->
      <div class="menu-toggle" @click="toggleSidebar" style="width: 100%;">
          <i style="color: black; position: absolute; left: 0;" class="fa fa-bars"></i>
      </div>

      <!-- Sidebar scroll-->
      <div class="scroll-sidebar">
          <!-- Sidebar navigation-->
          <nav class="sidebar-nav">
              <ul id="sidebarnav">
                <!-- <li v-on:click="SwapBackGroupColor('b6')" class="b6">
                        <router-link to="/DashboardPageData" class="waves-effect waves-dark b6" aria-expanded="false">
                            <i class="fa fa-tachometer"></i>
                            <span class="hide-menu">Dashboard</span>
                        </router-link>
                        </li> -->
                  <li class="b1" @click="SwapBackGroupColor('b1')">
                      <router-link to="/PlanUpdatePage" class="waves-effect waves-dark">
                          <i class="fa fa-plus"></i>
                          <span class="hide-menu">新增計劃</span>
                      </router-link>
                  </li>

                  <li class="b2" @click="SwapBackGroupColor('b2')">
                      <router-link to="/SearechProductUpdatePage" class="waves-effect waves-dark">
                          <i class="fa fa-box"></i>
                          <span class="hide-menu">搜尋產品</span>
                      </router-link>
                  </li>

                  <li class="b3" @click="SwapBackGroupColor('b3')">
                      <router-link to="/SearchLocationUpdatePage" class="waves-effect waves-dark">
                          <i class="fa fa-map-marker-alt"></i>
                          <span class="hide-menu">搜尋儲位</span>
                      </router-link>
                  </li>

                  <li class="b4" @click="SwapBackGroupColor('b4')">
                      <router-link to="/AllPlanUpdatePageTable" class="waves-effect waves-dark">
                          <i class="fa fa-paper-plane"></i>
                          <span class="hide-menu">搜尋計劃</span>
                      </router-link>
                  </li>
              </ul>
          </nav>
      </div>
  </aside>
</template>

<style scoped>
/* Sidebar mặc định */
.left-sidebar {
width: 250px;
height: 100vh;
background: rgba(255, 255, 255, 0.1);
box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
border-radius: 10px;
overflow: hidden;
transition: all 0.3s ease-in-out;
position: fixed;
left: 0;
top: 0;
z-index: 1000;
}

/* Khi sidebar thu nhỏ */
.left-sidebar.collapsed {
width: 60px;
}

/* Nút menu để ẩn/hiện sidebar */
.menu-toggle {
position: absolute;
top: 10px;
left: 15px;
cursor: pointer;
font-size: 24px;
color: white;
transition: transform 0.2s;
}

.menu-toggle:hover {
transform: scale(1.1);
}

/* Scroll sidebar */
.scroll-sidebar {
overflow-y: auto;
height: calc(100vh - 20px);
padding: 10px;
}

/* Sidebar navigation */
.sidebar-nav {
display: flex;
flex-direction: column;
align-items: flex-start;
}

#sidebarnav {
list-style: none;
padding: 0;
margin: 0;
width: 100%;
}

/* Sidebar items */
#sidebarnav li {
width: 100%;
transition: background 0.3s ease, transform 0.2s ease;
}

#sidebarnav li a {
display: flex;
align-items: center;
padding: 12px 15px;
color: rgb(14, 2, 2);
text-decoration: none;
font-weight: 500;
border-radius: 8px;
transition: background 0.3s ease, transform 0.2s ease;
}

/* Icon chỉnh đẹp hơn */
#sidebarnav li a i {
margin-right: 12px;
font-size: 18px;
transition: transform 0.2s ease-in-out;
}

/* Hover effect */
#sidebarnav li:hover {
background: rgba(255, 255, 255, 0.2);
transform: translateX(5px);
}

#sidebarnav li a:hover i {
transform: scale(1.1);
}

/* Click hiệu ứng màu */
#sidebarnav li.active {
background: linear-gradient(45deg, rgba(79, 232, 232, 0.7), rgba(0, 128, 255, 0.6));
}

/* Căn giữa nút ở cuối */
.text-center {
text-align: center;
margin-top: 20px;
}

/* Sidebar thu nhỏ */
.left-sidebar.collapsed .hide-menu {
display: none;
}

.left-sidebar.collapsed ul {
padding-left: 10px;
}

.left-sidebar.collapsed li a {
justify-content: center;
}

.left-sidebar.collapsed li a i {
margin-right: 0;
}
</style>


<script setup>
import { ref, watch, onMounted } from 'vue'
import {useRoute} from 'vue-router'

const route = useRoute()

onMounted(() => {
    checkDataPath(route.path)
})
watch(() => route.path, (newPath) => {
    checkDataPath(newPath)
})

const checkDataPath = (newPath) => {
    if(newPath === '/PlanUpdatePage')
        SwapBackGroupColor('b1')
    if(newPath === '/SearechProductUpdatePage')
        SwapBackGroupColor('b2')
    if(newPath === '/SearchLocationUpdatePage')
        SwapBackGroupColor('b3')
    if(newPath === '/AllPlanUpdatePageTable')
        SwapBackGroupColor('b4')
}
const isSidebarOpen = ref(true) // Trạng thái sidebar

const toggleSidebar = () => {
  isSidebarOpen.value = !isSidebarOpen.value
}

const isBackGroup = ref(null)

const SwapBackGroupColor = (data) => {
  if (isBackGroup.value !== null)
      document.querySelector('.' + isBackGroup.value).style.backgroundColor = 'transparent'

  document.querySelector('.' + data).style.backgroundColor = 'rgba(79, 232, 232, 0.5)'
  document.querySelector('.' + data).style.borderRadius = '20px'

  isBackGroup.value = data
}
</script>

