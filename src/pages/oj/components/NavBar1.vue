<template>
  <div id="header">
    <Menu theme="light" mode="horizontal" @on-select="handleRoute" :active-name="activeMenu" :class="oj-menu">
      <!-- <div class="logo"><span>{{website.website_name}}</span></div> -->
      <!-- <div class="logo" title="Luyện Code Online"><a href="/"><img src="/static/img/logo-ny.png" height="60px" alt="Luyện Code Online - Học lập trình tương tác trực tuyến"></a></div> -->
      <div class="logo" title="Luyện Code Online"><a href="/"><img src="/static/img/logo.png" height="60px" alt="Lỗi"></a></div>
      <Menu-item name="/problem">
        <Icon type="ios-keypad"></Icon>
        {{$t('m.NavProblems')}}
      </Menu-item>
      <Menu-item name="/contest" >
        <Icon type="md-trophy"></Icon>
        {{$t('m.Contests')}}
      </Menu-item>
      <Menu-item name="/status" >
        <Icon type="md-cloud-upload"></Icon>
        {{$t('m.NavStatus')}}
      </Menu-item>
      <Submenu name="rank">
        <template slot="title">
          <Icon type="md-podium"></Icon>
          {{$t('m.Rank')}}
        </template>
        <Menu-item name="/acm-rank">
          {{$t('m.ACM_Rank')}}
        </Menu-item>
        <Menu-item name="/oi-rank">
          {{$t('m.OI_Rank')}}
        </Menu-item>
        <Menu-item name="/experience-rank">
          {{$t('m.Experience_Rank')}}
        </Menu-item>
      </Submenu>
      <Submenu name="onlineapp">
        <template slot="title">
          <Icon type="ios-cloud" />
          {{$t('m.App')}}
        </template>
        <Menu-item name="/IDE">
          {{$t('m.IDE')}}
        </Menu-item>
        <Menu-item v-if="website.allow_forum_post" name="/Forum">
          {{$t('m.Forum')}}
        </Menu-item>
      </Submenu>
        <Submenu name="about">
        <template slot="title">
          <Icon type="md-information-circle"></Icon>
          {{$t('m.About')}}
        </template>
        <Menu-item name="/about">
          {{$t('m.AboutUs')}}
        </Menu-item>
        <Menu-item name="/judger">
          {{$t('m.Judger')}}
        </Menu-item>
        <Menu-item name="/FAQ">
          {{$t('m.FAQ')}}
        </Menu-item>
      </Submenu>

      <Dropdown @on-click="switchChange" class="ivu-menu-submenu">
        <div>
          <Icon type="ios-browsers"></Icon>
          &emsp;Giao diện
          <Icon type="ios-arrow-down"></Icon>
        </div>
        <DropdownMenu slot="list">
          <DropdownItem name="1"><Icon type="ios-browsers" color="#2d8cf0" />&emsp;Xanh lam</DropdownItem>
          <DropdownItem name="2"><Icon type="ios-browsers" color="#f58f98" />&emsp;Hồng</DropdownItem>
          <DropdownItem name="4"><Icon type="ios-browsers" color="#d63031" />&emsp;Đỏ</DropdownItem>
          <DropdownItem name="5"><Icon type="ios-browsers" color="#00b894" />&emsp;Xanh lá</DropdownItem>
          <DropdownItem name="3"><Icon type="ios-browsers" color="#673AB7" />&emsp;Tím</DropdownItem>
        </DropdownMenu>
      </Dropdown>
      
      <template v-if="!isAuthenticated">
        <div class="btn-menu">
          <Button
                  ref="loginBtn"
                  shape="circle"
                  @click="handleBtnClick('login')">{{$t('m.Login')}}
          </Button>
          <Button v-if="website.allow_register"
                  shape="circle"
                  @click="handleBtnClick('register')"
                  style="margin-left: 5px;">{{$t('m.Register')}}
          </Button>
        </div>
      </template>
      <template v-else>
        <Dropdown class="drop-menu" @on-click="handleRoute" placement="bottom" trigger="click">
          <Poptip trigger="hover" :title="`Cấp độ: ${ profile.grade }`" :content="`Điểm kinh nghiệm: ${ profile.experience }`" width="200px">
            <Tag v-if="profile.user.title" :color="profile.user.title_color" style="margin-right:-15px;">{{ profile.user.title }}</Tag>
            <Tag v-else :color="color" style="margin-right:-15px;">{{ gradename }}</Tag>
          </Poptip>
          <Button type="text" class="drop-menu-title">{{ user.username }}
            <Icon type="md-arrow-dropdown"></Icon>
          </Button>
          <Dropdown-menu slot="list">
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
