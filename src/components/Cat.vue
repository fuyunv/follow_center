<template>
  <div class="cat-bz">
    <div class="large monitor only">
      <div class="ui secondary vertical pointing menu loading">
        <slot></slot>
        <div class="ui basic segment add-newgod-bz internally celled grid addcat-bz" v-show="just_my">
          <a  @click="" href="javascript:void(0)" class="ui add-newgod-style" v-show="!add_cat_input_stat" @click="showAddCatInput">
            <i class="add icon"></i>{{ $t("AddCat.addnewcat") }}
          </a>
          <div v-show="add_cat_input_stat" class="ui action input row addcat-style-bz">
            <input @keyup.13="addCat" v-model="input_cat" id="id_add_cat" type="text" :placeholder="$t('AddCat.example')" class="eleven wide column">
            <div @click="addCat" class="ui button five wide column addcat-btn-bz">{{ $t("AddCat.add") }}&gt;</div>
          </div>
        </div>

        <router-link :to="{'name': route_name, params: {'cat': cat.cat}}" v-for="cat in cats" :class="{'active': $route.params.cat === cat.cat}" class="item">
          {{cat.cat}}
        </router-link>
      </div>
    </div>
    <!--
    <div class="ui form mobile only">
      <div class="field">
        <select v-model="$route.params.cat||''" @change="selectCat" class="ui dropdown">
          <option v-if="$route.params.cat === 'all'" selected disabled hidden value="all">请选择分类</option>
          <option v-if="$route.params.cat === 'recommand'" selected disabled hidden value="recommand">请选择分类</option>
          <option v-for="cat in cats" >{{cat.cat}}</option>
        </select>
      </div>
    </div>
    -->
  </div>
</template>

<script>
  import $ from 'jquery'
  export default {
    props: ['just_my', 'route_name'],
    computed: {
      cats: function () {
        if (this.just_my) {
          return this.$store.state.my_cats
        } else {
          return this.$store.state.cats
        }
      },
      link: function () {
        if (this.just_my) {
          return 'MyGods'
        } else {
          return 'Recommand'
        }
      }
    },
    components: {
    },
    data: function () {
      return {
        input_cat: '',
        add_cat_input_stat: false
      }
    },
    mounted () {
      this.$store.dispatch('getCat', this.just_my)
    },
    methods: {
      showAddCatInput: function () {
        this.add_cat_input_stat = true
        this.input_cat = '' // 清空上次的输入
        this.$nextTick(
          function () {
            $('#id_add_cat').focus()
          }
        )
      },
      addCat: function () {
        // window.alert(this.input_cat)
        this.cats.splice(0, 0, {cat: this.input_cat})
        this.add_cat_input_stat = false
        this.$router.push({name: this.route_name, params: {cat: this.input_cat}})
      },
      selectCat: function () {
        this.$router.push({name: this.route_name, params: {cat: this.$route.params.cat}})
      }
    }

  }
</script>

<style>
  #id_add_cat {
    box-shadow: none;
    border: none;
    background-color: transparent;
    padding: 1rem;
    border-bottom: 1px solid rgba(34,36,38,.15);
    border-radius: 0px;
  }
  #id_add_god {
    box-shadow: none;
    border: none;
    background-color: transparent;
    padding: 1rem;
    border-bottom: 1px solid rgba(34,36,38,.15);
    border-radius: 0px;
  }
  .ui.button.addcat-btn-bz {
    background-color: transparent;
  }
  .ui.input.row.addcat-style-bz {
    box-shadow: none;
    border: none;
    background-color: transparent;
  }
  .ui.celled.grid>.row>.column {
    box-shadow: none;
  }
  .ui.segment.add-newgod-bz:last-child {
    margin-top: 1.8rem;
  }
  .ui.segment.add-newgod-bz.celled.grid.addcat-bz {
    padding: 1rem 0rem;
  }
</style>
