<template>
  <div class="fly-header layui-bg-black">
    <div class="layui-container">
      <a class="fly-logo" href="/">
         <h4 class="moyu-title margin-none">
          考研 <span>社区</span>
          <!-- <span class="badge secondary">广东分办</span> -->
        </h4>
      </a>
      <ul class="layui-nav fly-nav layui-hide-xs">
        <li class="layui-nav-item layui-this">
          <a href="/">
            <i class="iconfont icon-jiaoliu"></i>交流
          </a>
        </li>
        <li class="layui-nav-item">
          <a href="http://localhost:8080/#/">
            <i class="iconfont icon-iconmingxinganli"></i>电子书
          </a>
        </li>
        <li class="layui-nav-item">
          <a href="http://localhost:8080/#/" target="_blank">
            <i class="iconfont icon-ui"></i>商城
          </a>
        </li>
      </ul>

      <ul class="layui-nav fly-nav-user">
        <!-- 未登入的状态 -->
        <template v-if="!isShow">
          <li class="layui-nav-item">
            <router-link class="iconfont icon-touxiang layui-hide-xs" to="/user123123"></router-link>
          </li>
          <li class="layui-nav-item">
            <router-link :to="{name: 'login'}">登入</router-link>
          </li>
          <li class="layui-nav-item">
            <router-link :to="{name: 'reg'}">注册</router-link>
          </li>
          <li class="layui-nav-item layui-hide-xs">
            <a
              href
              onclick="layer.msg('正在通过QQ登入', {icon:16, shade: 0.1, time:0})"
              title="QQ登入"
              class="iconfont icon-qq"
            ></a>
          </li>
          <li class="layui-nav-item layui-hide-xs">
            <a
              href
              onclick="layer.msg('正在通过微博登入', {icon:16, shade: 0.1, time:0})"
              title="微博登入"
              class="iconfont icon-weibo"
            ></a>
          </li>
        </template>

        <!-- 登入后的状态 -->
        <template v-else>
          <!-- 调整了Hover的区域 -->
          <li class="layui-nav-item" @mouseover="show()" @mouseleave="hide()">
            <router-link class="fly-nav-avatar" :to="{name: 'center'}">
              <cite class="layui-hide-xs">{{userInfo.name}}</cite>
              <!-- <i class="iconfont icon-renzheng layui-hide-xs" title="认证信息：layui 作者"></i> -->
              <i
                class="layui-badge fly-badge-vip layui-hide-xs"
                v-show="userInfo.isVip !== '0'"
              >VIP{{userInfo.isVip}}</i>
              <img :src="userInfo.pic" />
            </router-link>
            <dl
              class="layui-nav-child layui-anim layui-anim-upbit"
              :class="{'layui-show': isHover}"
            >
              <dd>
                <router-link :to="{name: 'info'}">
                  <i class="layui-icon">&#xe620;</i>基本设置
                </router-link>
              </dd>
              <dd>
                <router-link :to="{name: 'msg'}">
                  <i class="iconfont icon-tongzhi" style="top: 4px;"></i>我的消息
                </router-link>
              </dd>
              <dd>
                <router-link :to="{name: 'home', params: {uid: userInfo._id}}">
                  <i class="layui-icon" style="margin-left: 2px; font-size: 22px;">&#xe68e;</i>我的主页
                </router-link>
              </dd>
              <hr style="margin: 5px 0;" />
              <dd>
                <a href="javascript: void(0)" style="text-align: center;" @click="logout()">退出</a>
              </dd>
            </dl>
          </li>
          <div class="fly-nav-msg" v-show="num.message && num.message !== 0">{{num.message}}</div>
          <transition name="fade">
            <div class="layui-layer-tips" v-show="hasMsg">
              <div class="layui-layer-content">
                您有{{num.message}}条未读消息
                <i class="layui-layer-TipsG layui-layer-TipsB"></i>
              </div>
            </div>
          </transition>
        </template>
      </ul>
    </div>
  </div>
</template>

<script>
import { mapState } from 'vuex'
export default {
  name: 'Header',
  data () {
    return {
      isHover: false,
      hoverCtrl: {},
      hasMsg: false
    }
  },
  methods: {
    show () {
      // 当用户的鼠标移入头像的时候，去显示操作菜单
      clearTimeout(this.hoverCtrl)
      this.hoverCtrl = setTimeout(() => {
        this.isHover = true
      }, 200)
    },
    hide () {
      // 当用户的鼠标移出头像的时候，去隐藏操作菜单
      clearTimeout(this.hoverCtrl)
      this.hoverCtrl = setTimeout(() => {
        this.isHover = false
      }, 500)
    },
    logout () {
      this.$confirm(
        '确定退出吗？',
        () => {
          localStorage.clear()
          this.$store.commit('setToken', '')
          this.$store.commit('setUserInfo', {})
          this.$store.commit('setIsLogin', false)
          this.$router.push({ name: 'index' }, () => {})
        },
        () => {}
      )
    }
  },
  watch: {
    num (newval, oldval) {
      if (newval.event && newval !== oldval) {
        // 判断消息数量
        if (newval.message && newval.message > 0) {
          this.hasMsg = true
          setTimeout(() => {
            this.hasMsg = false
          }, 2000)
        }
      }
    }
  },
  computed: {
    // num () {
    //  return this.$store.state.num
    // }
    ...mapState({
      num: (state) => state.num
    }),
    isShow () {
      return this.$store.state.isLogin
    },
    userInfo () {
      return (
        this.$store.state.userInfo || {
          name: '',
          pic: '',
          isVip: '0'
        }
      )
    }
  }
}
</script>

<style lang="scss" scoped>
.fly-logo {
  left: -15px;
  top: -10px;
  margin-left: 15px;
}
.layui-layer-tips {
  position: absolute;
  white-space: nowrap;
  right: 0;
  top: 60px;
  z-index: 2000;
}
.moyu-title {
    display: flex;
    color: #fff;
    align-items: center;
    font-size: 30px;
    margin-bottom: 5px;
    margin-top:19px;
    margin-left: -4px;
    font-weight: bolder;
    span {
      display: flex;
      align-items: center;
      height: 38px;
      background: #0071de;
      border-radius: 3px;
      color: #fff;
      margin-left: 4px;
      font-size: 26px;
      padding: 0px 5px;
    }
  }
</style>
