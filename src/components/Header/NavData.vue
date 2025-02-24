<template>
    <aside class="left-sidebar">
            <!-- Sidebar scroll-->
            <div class="scroll-sidebar">
                <!-- Sidebar navigation-->
                <nav class="sidebar-nav">
                    <ul id="sidebarnav">
                        <!-- <li> <a class="waves-effect waves-dark" href="index.html" aria-expanded="false"><i
                                    class="fa fa-tachometer"></i><span class="hide-menu">Dashboard</span></a>
                        </li> -->
                        
                        <li class="b1" v-on:click="SwapBackGroupColor('b1')">
                          <router-link to="/PlanUpdatePage" class="waves-effect waves-dark" aria-expanded="false">
                            <i class="fa fa-paper-plane" aria-hidden="true"></i>
                            <span class="hide-menu">{{ t('Plan') }}</span>
                        </router-link>
                        </li>

                        <li class="b2" v-on:click="SwapBackGroupColor('b2')">
                          <router-link to="/SearechProductUpdatePage" class="waves-effect waves-dark" aria-expanded="false">
                            <i class="fa fa-search" aria-hidden="true"></i>
                            <span class="hide-menu">{{ t('Search Product By Code') }}</span>
                        </router-link>
                        </li>

                        <li class="b3" v-on:click="SwapBackGroupColor('b3')">
                          <router-link to="/SearchLocationUpdatePage" class="waves-effect waves-dark" aria-expanded="false">
                            <i class="fa fa-search" aria-hidden="true"></i>
                            <span class="hide-menu">{{ t('Search Location By Code') }}</span>
                        </router-link>
                        </li>

                        <li class="b4" v-on:click="SwapBackGroupColor('b4')">
                          <router-link to="/AllPlanUpdatePage" class="waves-effect waves-dark" aria-expanded="false">
                            <i class="fa fa-paper-plane" aria-hidden="true"></i>
                            <span class="hide-menu">{{ t('Find All Plan') }}</span>
                        </router-link>
                        </li>
                        <!----<li> <a class="waves-effect waves-dark" href="pages-error-404.html" aria-expanded="false"><i
                                    class="fa fa-question-circle"></i><span class="hide-menu">404</span></a>
                        </li> ---->
                    </ul>
                    <!-- <div class="text-center mt-4">
                    <select v-model="selectedLanguage" @change="changeLanguage" class="btn waves-effect waves-light btn-info hidden-md-down text-white">
                      <option value="en">English</option>
                      <option value="vi">Tiếng Việt</option>
                      <option value="zh">中文</option>
                    </select>
                  </div> -->

                </nav>
                <!-- End Sidebar navigation -->
            </div>
            <!-- End Sidebar scroll-->
        </aside>
</template>

<style scoped>
/* Thiết lập Glassmorphism */
.left-sidebar {
  width: 250px;
  height: 100vh;
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
  border-radius: 10px;
  overflow: hidden;
  transition: all 0.3s ease-in-out;
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

/* Nút trong sidebar */
.text-center a {
  display: inline-block;
  padding: 10px 20px;
  background: linear-gradient(45deg, #4fe8e8, #0080ff);
  color: white;
  font-weight: bold;
  border-radius: 8px;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.text-center a:hover {
  transform: scale(1.05);
  box-shadow: 0 4px 15px rgba(0, 128, 255, 0.4);
}

</style>

<script setup>
  import { ref } from 'vue'
  import { useI18n } from 'vue-i18n' // Import vue-i18n

  const isBackGroup = ref(null)

  const { t, locale } = useI18n()  // Sử dụng i18n để thay đổi ngôn ngữ


  const SwapBackGroupColor = (data) => {
    if (isBackGroup.value !== null)
        document.querySelector('.' + isBackGroup.value).style.backgroundColor = 'transparent'

    document.querySelector('.' + data).style.backgroundColor = 'rgba(79, 232, 232, 0.5)'

    isBackGroup.value = data
  }


  // Lưu trữ ngôn ngữ đã chọn
  const selectedLanguage = ref('zh')

  // Hàm thay đổi ngôn ngữ
  // const changeLanguage = () => {
  //   locale.value = selectedLanguage.value // Cập nhật ngôn ngữ bằng vue-i18n
  // }
</script>
