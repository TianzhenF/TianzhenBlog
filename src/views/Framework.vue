<template>
  <div class="layout">
    <el-container>
      <el-header class="header">
        <div class="logo">EasyBlog</div>
        <div class="user_info">
          <span>欢迎回来,</span>
          <el-dropdown trigger="click">
            <span class="nick_name">
              {{userInfo.nickName}}
              <span class="iconfont icon-close"></span>
            </span>
           
            <template #dropdown>
              <el-dropdown-menu>
                <el-dropdown-item>个人信息</el-dropdown-item>
                <el-dropdown-item>退出</el-dropdown-item>
              </el-dropdown-menu>
            </template>
          </el-dropdown>
          <div class="avatar"><img :src="userInfo.avatar"></div>
        </div>
      </el-header>
      <el-container class="container">
        <el-aside width="200px" class="left_Aside">
          <div>
            <el-button class="post_btn">发布</el-button>
          </div>
          <ul class="menu_panel">
            <li v-for="(menu, index) in menuList" :key="index">
              <span class="menu_title_p" @click="openClose(index)">
                <span :class="['iconfont', menu.icon]"></span>
                <span class="menu_title">{{ menu.title }}</span>
                <span :class="['iconfont', 'open-close', menu.open ? 'icon-close' : 'icon-open']"></span>
              </span>
              <ul class="sub_menu" v-if="menu.open">
                <li v-for="subMenu in menu.children" :key="subMenu">
                  <span ></span>
                  <router-link :to="subMenu.path"
                   :class="['sub_menu_item',activePath==subMenu.path?'active':'']"
                    >{{ subMenu.title }}</router-link>
                </li>
              </ul>
            </li>
          </ul>
        </el-aside>
        <el-main class="right_main">
          <router-view></router-view>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script setup>
import { reactive, ref } from "@vue/reactivity";
import { getCurrentInstance, watch } from "@vue/runtime-core";
import VueCookies from 'vue-cookies';
import {useRoute, useRouter} from "vue-router";

const route = useRoute();
const router = useRouter();
const {proxy} = getCurrentInstance();

const menuList = ref([
  {
    title: "博客",
    icon: "icon-blog",
    open: true,
    children: [
      {
        title: "博客管理",
        path: "/blog/list",
      },
      {
        title: "分类管理",
        path: "/blog/category",
      },
    ],
  }, {
    title: "专题",
    icon: "icon-zhuanti",
    open: true,
    children: [
      {
        title: "专题管理",
        path: "/special/category",
      },
    ],
  }, {
    title: "设置",
    icon: "icon-settings",
    open: true,
    children: [
      {
        title: "个人信息设置",
        path: "/setting/my",
      },
      {
        title: "博客成员",
        path: "/setting/user",
      },
      {
        title: "系统管理",
        path: "/setting/sysSetting",
        roleType: 1
      },

    ],
  },
  {
    title: "回收站",
    icon: "icon-delete",
    open: true,
    children: [
      {
        title: "回收站",
        path: "/recovery/list",
      },
    ],
  },
]);
const openClose = (index) => {
  const open = menuList.value[index].open;
  menuList.value[index].open = !open;
}
const userInfo = ref({});
const init = ()=>{
  userInfo.value = VueCookies.get("userInfo");
  userInfo.value.avatar = proxy.globalInfo.imageUrl + userInfo.value.avatar;
  console.log(userInfo.value);
}
init();

const activePath = ref(null);

watch(route,(newVal,oldVal) => {
  console.log(newVal.path);
  activePath.value = newVal.path;
},{immediate:true,deep:true});

</script>

<style lang="scss">
.layout {
  .header {
    border-bottom: 1px solid #ddd;
    display: flex;
    align-items: center;
    justify-content: space-between;
    .logo {
    font-size: 30px;
  }
  .user_info{
    display: flex;
    align-items: center;
    .nick_name{
      cursor: pointer;
      color: rgba(6, 143, 234);
    }
    .icon-close{
      font-size: 14px;
    }
  }
  .avatar{
    width: 40px;
    margin-left: 10px;
    img{
      width: 100%;
      border-radius: 20px;
    }
  }
  }
}

.container {
  background: #f5f6f7;
  height: calc(100vh - 60px);
  padding-top: 10px;

  .left_Aside {
    width: 280px;
    padding: 0px 15px;

    .post_btn {
      background: green;
      color: #fff;
      height: 40px;
      width: 100%;
    }

    .menu_panel {
      padding: 0px;
      margin: 0px;
      margin-top: 5px;

      ul,
      li {
        padding: 0px;
        margin: 0px;
        list-style: none;
      }

      .menu_title_p {
        padding: 0px 5px;
        line-height: 45px;
        display: flex;
        cursor: pointer;

        .iconfont {
          font-size: 18px;
          color: rgb(155, 151, 151);
        }
      }

      .menu_title {
        flex: 1;
        color: rgb(75, 74, 74);
        margin-left: 10px;
      }

      .open-close {
        width: 20px;
        font-size: 16px;
      }
    }

    .sub_menu {
      font-size: 14px;

      .sub_menu_item {
        padding: 0px 10px 0px 33px;
        cursor: pointer;
        display: block;
        line-height: 40px;
        text-decoration: none;
        color: #3f4042;
      }

      .sub_menu_item:hover {
        background: #ddd;
      }
      .active{
        background: #ddd;
        color: rgb(214, 103, 103);
      }
    }

    .menu_title_p:hover {
      background: #ddd;
    }
  }

  .right_main {
    background: #fff;

  }
}
</style>