<template>
  <div>
    <button @click="confirm" class="ui circular icon basic button large monitor only un-read-bz" :data-content="$t('UnRead.unread')">
      <div data-content="Add users to your feed">
        {{unread_message_count}}
      </div>
    </button>
    <div class="ui modal update-last-bz">
      <div class="content">
        <p>今天以前的资讯不看了?</p>
      </div>
      <div class="actions">
        <div @click="updateLast" class="ui approve button follow-button button-to-follow-bz keppel">确定</div>
        <div class="ui cancel button follow-button button-to-follow-bz">取消</div>
      </div>
    </div>
  </div>
</template>
<script>
  import '../assets/mobile.css'

  import $ from 'jquery'
  export default {
    props: [],
    components: {
    },
    computed: {
      unread_message_count () {
        return this.$store.state.unread_message_count
      }
    },
    data: function () {
      return {
      }
    },
    mounted () {
      $(this.$el).popup()
    },
    methods: {
      updateLast: function () {
        let now = (new Date()).setHours(0, 0, 0, 0)
        this.$store.dispatch('recordLastMessage', now).then(function (data) {
          window.location.reload()
        })
      },
      confirm: function () {
        $($('.update-last-bz')[0]).modal('show')
      }
    }
  }
</script>
<style>
  .un-read-bz {
    position: fixed;
    right: 1.5em;
    border: 1px solid #DADADA;
    bottom: 5em;
  }
  @media (min-width: 1100px) and (max-width: 1300px) {
    .un-read-bz {
      right: .5em;
    }
  }
</style>
