<template>
  <div id='app' class="main container" >
    <header class="main-header">
      <div class="ui borderless main menu bar-above">
        <div class="ui container">
          <a @click="backToMain" href="javascript:;" class="header item logo-font-bz no-highlight">
            <img v-show="show_bar" class="logo first-logo" src="../static/assets/logo.svg">
            <div id="header">Follow Center</div>
          </a>
          <div class="right menu ">
            <div class="item large monitor only">
              <form @keyup.13="search" v-on:submit.prevent class="ui  transparent icon input">
                <input v-model="key" type="text" :placeholder="$t('App.search')">
                <i @click="search" class="search link icon"></i>
              </form>
            </div>

            <div v-show="user_name" class="ui simple dropdown item user-imfor-bz">
              <img :src="avatar" class="ui avatar image">
              <div class="menu login-menu-bz">
                <a @click="$router.push({ name: 'UserSet'})" class="item">{{ $t("App.settings") }}</a>
                <a @click="$router.push({ name: 'God', params: { god_name: user_name }})" class="item">{{ $t("App.mine") }}</a>
                <a @click="logout" class="item">{{ $t("App.logout") }}</a>
              </div>
            </div>
            <a v-show="user_name===''" href="/login.html" class="item">
              {{ $t("App.login") }}
            </a>
          </div>
        </div>
      </div>
      <nav v-show="show_bar" class="ui borderless main menu fix-bz bar-blow">
        <div class="ui container bar-selection">
          <router-link :to="{'name': 'Recommand'}" :class="{'active': this.$route.name==='Recommand'}" class="item navi-bz move-left-bz">{{ $t("App.whattofollow") }}</router-link>
          <router-link v-show="user_name!=''" :to="{ name:'MyGods'}" :class="{'active': this.$route.name==='MyGods'}" class="item navi-bz">{{ $t("App.following") }}</router-link>
          <router-link v-show="user_name!=''" :to="{ name:'Collect'}" :class="{'active': this.$route.name==='Collect'}" class="item navi-bz">{{ $t("App.saved") }}</router-link>
          <router-link :to="{ name:'Bio'}" :class="{'active': this.$route.name==='Bio'}" class="item navi-bz">{{ $t("App.biography") }}</router-link>
        </div>
      </nav>
    </header>
    <router-view :call_back="login_call_back"></router-view>
  </div>
</template>
<script>
  import $ from 'jquery'
  import store from './store'
  import NProgress from 'nprogress'
  import checkLogin from 'bz-lib/functions/checkLogin'
  export default {
    store,
    data () {
      return {
        key: '',
        scroll_wait: false // 不让checkBar因为触发太多次而影响效率
      }
    },
    components: {
    },
    watch: {
      'unread_message_count': function (val, oldVal) {
        if (this.unread_message_count === 0) {
          document.title = 'Follow your dreams'
        } else {
          document.title = `(${this.unread_message_count}) Follow your dreams`
        }
      },
      'loading': function (val, oldVal) {
        if (val) {
          NProgress.start()
        } else {
          NProgress.done()
        }
      }
    },
    computed: {
      show_bar () {
        return this.$store.state.show_bar
      },
      loading () {
        return store.state.p.loading
      },
      unread_message_count () {
        return store.state.unread_message_count
      },
      user_name () {
        return store.state.p.user_info.user_name
      },
      avatar () {
        return store.state.p.user_info.picture
      }
    },
    mounted () {
      this.$store.commit('CHECK_LOGIN')
      if (checkLogin()) { this.$store.dispatch('getUserInfo') }
      this.$nextTick(function () {
        $('.fix-bz').visibility(
          {
            type: 'fixed'
          }
        )
        $('.first-logo').visibility(
          {
            once: false,
            onTopPassed: function () {
              var y = $('.first-logo').offset().left
              $('.first-logo').css('left:' + y)
              $('.first-logo').addClass('fixed')
              $('#header').addClass('padding-left-bz')
              $('.move-left-bz').addClass('move-titile')
            },
            onTopPassedReverse: function () {
              // this.checkBar()
              // this.show_bar = true
              $('.first-logo').removeClass('fixed')
              $('#header').removeClass('padding-left-bz')
              $('.move-left-bz').removeClass('move-titile')
            }
          }
        )
        this.$on('checkBar',
          function () {
            if (this.scroll_wait) return
            else {
              this.checkBar()
              this.scroll_wait = true
              let _this = this
              window.setTimeout(
                function () {
                  _this.scroll_wait = false
                }
              , 200)
            }
          }
        )
      })
    },
    methods: {
      logout: function () {
        if (window.bz_url) {
          window.localStorage.removeItem('user_id')
          window.location.href = 'login.html'
        } else {
          window.location.href = '/api_logout'
        }
      },
      login_call_back: function () {
        this.$router.go({name: 'Main'})
      },
      search: function () {
        if (this.key) {
          store.state.search_messages = [] // 清空上次的查找
          this.$router.push({name: 'Search', params: {key: this.key}})
          console.log('fucnk')
        } else {
          this.$router.push({name: 'Main'})
        }
      },
      backToMain: function () {
        this.$router.push({name: 'Main'})
        // $('html, body').animate(
          //   {
            //     scrollTop: '0'
            //   }, 500
        // )
      }
    }
  }
</script>
<style>
  body {
    font-family: "Lato", "arial","Microsoft YaHei","sans-serif";
    line-height: 1.5;
    font-weight: normal;
    background-color: #fafafa;
    color: rgba(0,0,0,.76);
  }
  /* 必需 */
  .expand-transition {
    transition: all .3s ease;
    overflow: hidden;
  }

  /* .expand-enter 定义进入的开始状态 */
  /* .expand-leave 定义离开的结束状态 */
  .expand-enter, .expand-leave {
    height: 0;
    padding: 0;
    opacity: 0;
  }
  a {
    color: #2EA974;
    -webkit-transition: all 0.3s ease;
    -moz-transition: all 0.3s ease;
    -ms-transition: all 0.3s ease;
    -o-transition: all 0.3s ease;
    transition: all 0.3s ease;
  }
  a:hover, a:focus, a:active {
    color: #168454;
  }
  /* 全局的人物名字---------------------------- */
  .user-name-a {
    color: rgba(0,0,0,.8);
    transition: color 0.3s linear;
  }
  .user-name-a:hover {
    color: rgba(0,0,0,.6);
  }
  /* 设置社交图标------------------------------ */
  i.icon.god-icon-bz {
    transition: color 0.3s;
    color: #999999;
  }
  .github.light-bz {
    color: rgba(0,0,0,0.7);
  }
  .twitter.light-bz {
    color: #41ABE1;
  }
  .instagram.light-bz {
    color: #7E4532;
  }
  .facebook.light-bz {
    color: #3B5998;
  }
  .tumblr.light-bz {
    color: #36465D;
  }
  i.icon.github.god-icon-bz:hover {
    color: rgba(0,0,0,0.7);
  }
  i.icon.twitter.god-icon-bz:hover {
    color: #41ABE1;
  }
  i.icon.instagram.god-icon-bz:hover {
    color: #7E4532;
  }
  i.icon.tumblr.god-icon-bz:hover {
    color: #36465D;
  }
  i.icon.facebook.god-icon-bz:hover {
    color: #3B5998;
  }
  .god-icon-bz a:hover {
    color: #888888;
  }
  /* 设置导航-------------------------------- */
  .ui.menu.bar-above {
    margin-bottom: 0;
    border: none;
    box-shadow: none;
    height: 65px;
  }
  .ui.menu .header.item.logo-font-bz div {
    font-size: 1.5em;
    font-weight: 600;
    padding-left: 0.5em;
  }
  .ui.menu.bar-blow {
    margin-top: 0;
    border: none;
    height: 50px;
    box-shadow: 1px 1px 1px rgba(0,0,0,.09);
    border-radius: 0em;
  }
  .ui.bar-selection {
    border-top: 0.5px solid rgba(0,0,0,.05);
  }
  .ui.menu .item.navi-bz {
    padding: 1em 2em;
    color: rgba(0,0,0,.5);
  }
  .ui.menu .item.navi-bz:hover {
    color: rgba(0,0,0,.7);
  }
  .first-logo.fixed {
    position: fixed;
    top: 13px;
    z-index: 999999;
  }
  .ui.menu a.item.no-highlight:hover {
    background:inherit;
    color:inherit;
  }
  .ui.menu .header.item.logo-font-bz .padding-left-bz {
    padding-left: 2.16em;
  }
  .move-left-bz.move-titile {
    margin-left: 4em;
  }
  .ui.menu .dropdown.item .menu.login-menu-bz {
    border-radius: 0.06em;
    border: none;
    box-shadow: 0 0 0 1px rgba(0, 0, 0, 0.05), 0 1px 3px 0 rgba(0, 0, 0, 0.13);
  }
  @media (max-width : 767px) {
    .ui.menu .item.user-imfor-bz {
      padding: 1rem 2rem;
    }
  } 
</style>
