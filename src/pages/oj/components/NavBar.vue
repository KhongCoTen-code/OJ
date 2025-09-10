<template>
  <div id="header">
    <Menu theme="light" mode="horizontal" @on-select="handleRoute" :active-name="activeMenu" :class="oj-menu">
      <!-- <div class="logo"><span>{{website.website_name}}</span></div> -->
      <!-- <div class="logo" title="Luyện Code Online"><a href="/"><img src="/static/img/logo-ny.png" height="60px" alt="Luyện Code Online - Học lập trình tương tác trực tuyến"></a></div> -->
      <div class="logo" title=""><a href="/"><img src="/static/img/logo.png" height="60px" alt="Lỗi"></a></div>
      
      <Menu-item name="/problem" class="white">
        <Icon type="ios-keypad" class="iconhover" ></Icon>
        {{$t('m.NavProblems')}}
      </Menu-item>
      <Menu-item name="/contest" class="white">
        <Icon  type="md-trophy" class="iconhover"></ Icon>
        {{$t('m.Contests')}}
      </Menu-item>
      <Menu-item name="/status "class="white">
        <Icon  type="ios-pulse" class="iconhover"></ Icon>
        {{$t('m.NavStatus')}}
      </Menu-item>
      <Submenu name="rank" class="white">
        <template slot="title">
          <Icon  type="md-podium" class="iconhover"></ Icon>
          {{$t('m.Rank')}}
        </template>
        <Menu-item name="/acm-rank">
          {{$t('m.ACM_Rank')}}
        </Menu-item>
        <Menu-item name="/oi-rank">
          {{$t('m.OI_Rank')}}
        </Menu-item>
        <Menu-item name="/experience-rank">
          {{$t('m.Experience_Ranklist')}}
        </Menu-item>
      </Submenu>
      <Submenu name="onlineapp" class="white">
        <template slot="title">
          <Icon type="ios-cloud" class="iconhover" /> 
          {{$t('m.App')}}
        </template>
        <Menu-item name="/IDE" >
          {{$t('m.IDE')}}
        </Menu-item>
        <Menu-item v-if="website.allow_forum_post" name="/Forum">
          {{$t('m.Forum')}}
        </Menu-item>
      </Submenu>
      <Submenu name="about" class="white">
        <template slot="title">
          <Icon type="md-informatio n-circle" class="iconhover"></Icon>
          {{$t('m.About')}}
        </template>
        <Menu-item name="/about">
          {{$t('m.Judger')}}
        </Menu-item>
        <Menu-item name="/FAQ">
          {{$t('m.FAQ')}}
        </Menu-item>
        <Menu-item name="/AboutUs">
          {{$t('m.AboutUs')}}
        </Menu-item>
      </Submenu>

      <Dropdown @on-click="switchChange" class="ivu-menu-submenu white">
        <div>
          <Icon type="ios-browsers" class="iconhover"></Icon>
          &emsp;Đổi giao diện
          <Icon type="ios-arrow-down"></Icon>
        </div>
        <DropdownMenu slot="list" >
          <DropdownItem name="1"><Icon type="ios-browsers" color="#2d8cf0" />&emsp;Xanh lam</DropdownItem>
          <DropdownItem name="2"><Icon type="ios-browsers" color="#f58f98" />&emsp;Hồng</DropdownItem>
          <DropdownItem name="4"><Icon type="ios-browsers" color="#d63031" />&emsp;Đỏ</DropdownItem>
          <DropdownItem name="5"><Icon type="ios-browsers" color="#00b894" />&emsp;Xanh lá</DropdownItem>
          <DropdownItem name="3"><Icon type="ios-browsers" color="#673AB7" />&emsp;Tím</DropdownItem>
        </DropdownMenu>
      </Dropdown>

      <template v-if="!isAuthenticated">
        <div class="btn-menu">
          <Button type="ghost"
                  ref="loginBtn"
                  shape="circle"
                  @click="handleBtnClick('login')">{{$t('m.Login')}}
          </Button>
          <Button v-if="website.allow_register"
                  type="ghost"
                  shape="circle"
                  @click="handleBtnClick('register')"
                  style="margin-left: 5px;">{{$t('m.Register')}}
          </Button>
        </div>
      </template>
      <template v-else>
        <Dropdown class="drop-menu" @on-click="handleRoute" placement="bottom" trigger="click">
          <Poptip trigger="hover" :title="`Cấp hiện tại: ${ profile.grade }`" :content="`Điểm kinh nghiệm: ${ profile.experience }`" width="200px">
            <Tag v-if="profile.user.title" :color="profile.user.title_color" style="margin-right:-15px;">{{ profile.user.title }}</Tag>
            <Tag v-else :color="color" style="margin-right:-15px;">{{ gradename }}</Tag>
          </Poptip>
          <Button type="text" class="drop-menu-title">{{ user.username }}
            <Icon type="md-arrow-dropdown"></Icon>
          </Button>
          <Dropdown-menu slot="list" class="white">
            <Dropdown-item name="/user-home">{{$t('m.MyHome')}}</Dropdown-item>
            <Dropdown-item name="/status?myself=1">{{$t('m.MySubmissions')}}</Dropdown-item>
            <Dropdown-item name="/setting/profile">{{$t('m.Settings')}}</Dropdown-item>
            <Dropdown-item v-if="isAdminRole" name="/admin">{{$t('m.Management')}}</Dropdown-item>
            <Dropdown-item divided name="/logout">{{$t('m.Logout')}}</Dropdown-item>
          </Dropdown-menu>
        </Dropdown>
      </template>
    </Menu>
    <Modal v-model="modalVisible" :width="400">
      <div slot="header" class="modal-title">{{$t('m.Welcome_to')}} {{website.website_name_shortcut}}</div>
      <component :is="modalStatus.mode" v-if="modalVisible"></component>
      <div slot="footer" style="display: none"></div>
    </Modal>
  </div>
</template>

<script>
  import { mapGetters, mapActions } from 'vuex'
  import login from '@oj/views/user/Login'
  import register from '@oj/views/user/Register'

  export default {
    components: {
      login,
      register
    },
    mounted () {
      this.getProfile()
    },
    methods: {
      ...mapActions(['getProfile', 'changeModalStatus']),
      handleRoute (route) {
        if (route && route.indexOf('admin') < 0) {
          this.$router.push(route)
        } else {
          window.open('/admin/')
        }
      },
      handleBtnClick (mode) {
        this.changeModalStatus({
          visible: true,
          mode: mode
        })
      },
      // Đổi chủ đề
      switchChange (status) {
        let params = document.getElementById('app')
        params.className = 'theme' + status
        window.localStorage.setItem('app', document.getElementById('app').className)
      },
      // Lưu chủ đề vào localStorage
      localStorageDate () {
        let memoryColor = window.localStorage.getItem('app')
        let params = document.getElementById('app')
        if (memoryColor) {
          params.className = memoryColor
        }
      }
    },
    computed: {
      ...mapGetters(['website', 'modalStatus', 'user', 'profile', 'isAuthenticated', 'isAdminRole', 'color', 'gradename']),
      // Theo đường dẫn hiện tại
      activeMenu () {
        return '/' + this.$route.path.split('/')[1]
      },
      modalVisible: {
        get () {
          return this.modalStatus.visible
        },
        set (value) {
          this.changeModalStatus({visible: value})
        }
      }
    },
    created () {
      this.localStorageDate()
    }
  }
</script>


<style lang="less" scoped>

.white{
  color: white !important;
}

.iconhover:hover, .white:hover {
  color: #36ebf8 !important;
}

#header {
  min-width: 300px;
  position: fixed;
  top: 0;
  left: 0;
  height: 60px; // Đặt chiều cao cố định
  width: 100%;
  z-index: 1000;
  background-color: #0091CE;
  box-shadow: 0 1px 5px 0 rgba(0, 0, 0, 0.1);

  .oj-menu, .ivu-menu, .ivu-menu-horizontal {
    background: #0091CE;
    height: 60px !important; // Đặt chiều cao menu
    line-height: 60px !important; // Căn giữa các item
    border-bottom: none !important; // Xóa đường kẻ mặc định
    margin-bottom: 0 !important; // Xóa khoảng trắng dưới
    box-shadow: none !important;
  }

  .logo {
    margin-left: 2%;
    margin-right: 2%;
    font-size: 20px;
    float: left;
    line-height: 56px;
    img {
      vertical-align: middle;
      // height: 40px; // Giảm chiều cao logo
    }
  }

  .drop-menu {
    float: right;
    margin-right: 30px;
    position: absolute;
    right: 10px;
    &-title {
      font-size: 18px;
    }
  }
  .btn-menu {
    font-size: 16px;
    float: right;
    margin-right: 10px;
  }
  .change-menu {
    float: right;
    margin-right: 130px;
    position: absolute;
    right: 10px;
    &-title {
      font-size: 18px;
    }
  }
}

.modal {
  &-title {
    font-size: 18px;
    font-weight: 600;
  }
}

.ivu-btn-ghost {
  color: #36ebf8;
}

// .drop-menu .ivu-btn-text,
// .drop-menu .ivu-btn-text:hover,
// .drop-menu .ivu-btn-text:focus,
// .drop-menu .ivu-btn-text:active {
//   color: #fff !important;
//   background: transparent !important;
// }
</style>

