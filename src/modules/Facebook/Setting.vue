<template >
  <div class="container-fluid">
    <div class="my-3">
      <div class="container-fluid d-flex justify-content-between">
        Facebook Page: Increment Technologies
        <div>
          <button type="button" id="btn" class="btn btn-info"  @click="save()" >Save</button>
          <b-button id="btn" variant="outline-info"  @click="setting.configure = !setting.configure ">Configure</b-button>
        </div>
      </div>
      <div class="container-fluid mt-3">
        <p>Get Started</p>
        <div class="d-flex justify-content-between">
          Enable to allow user click the get started button and reply
          the welcome message
          <b-button id="btn" variant="outline-info"  @click="setting.enable = !setting.enable ">Enable</b-button>
        </div>
      </div>
    </div>
    <div
      class="container-fluid"
    >
      <div class="row">
        <div class="col-lg-4" 
        v-for="(menu,i) in setting.menus"
        :key="i"
        @dragstart="startDrag($event,i)"
        @drop="onDrop($event,i)"
        @dragover.prevent
        @dragenter.prevent
        >
          <div class="border mb-3">
            <div class="card border-0 px-3 py-1">
              <div class="card-title d-flex justify-content-between d-inline-flex align-items-center  ">
                <div>{{menu.name}} 
                  <a
                    data-toggle="collapse"
                    :href="'#'+ i"
                    role="button"
                    aria-expanded="false"
                    :aria-controls="i"
                  >
                    <!-- <i class="fa fa-chevron-up mr-3"></i> -->
                  </a>
                </div> 
                <!-- <span class="d-inline-flex align-items-center "> -->
                  <!-- <a
                    data-toggle="collapse"
                    :href="'#'+ i"
                    role="button"
                    aria-expanded="false"
                    :aria-controls="i"
                  >
                    <i class="fa fa-chevron-up mr-3"></i>
                  </a> -->
                  <div class="d-flex justify-content-between d-inline-flex align-items-center ">
                    <i class="fa fa-chevron-up mr-3"></i>
                    <div class="border border-primary rounded-circle py-2 px-3 mt-2">
                     {{i+1}}
                    </div>
                  </div>
                <!-- </span> -->
              </div>
            </div>
            <div class="collapse show" :id="i">
              <b-card class="border-0">
                Title
                <b-form-input v-model="menu.name" list="my-list-id" placeholder="Rooms"></b-form-input>
                <b-card-text>
                  <br>Payload
                  <br>
                  <b-form-input list="my-list-id" placeholder="Payload"></b-form-input>
                </b-card-text>
              </b-card>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import ROUTER from 'src/router'
import Vue from 'vue'
import AUTH from 'src/services/auth'
import COMMON from 'src/common.js'
export default {
  data: () => {
    return {
      user: AUTH.user,
      setting: {
        configure: false,
        enable: false,
        menus: [
          { id: 1, name: 'Menu 1', payload: null },
          { id: 2, name: 'Menu 2', payload: null },
          { id: 3, name: 'Menu 3', payload: null }
        ]
      }
    }
  },
  mounted() {
    this.retrieve()
  },
  computed: {
    returnSetting () {
      return this.setting
    }
  },
  methods: {
    retrieve(){
      console.log(JSON.stringify(this.setting))
      let parameter = {
        account_id: this.user.userID
      }
      $('#loading').css({display: 'block'})
      this.APIRequest('bot_template/retrieve', parameter).then(response => {
        $('#loading').css({display: 'none'})
      })
      .catch(err => console.log(err))
    },
    save(){
      console.log(this.setting)
      if(this.setting.menus !== this.setting.menus){
        $('#loading').css({display: 'block'})
        this.APIRequest('bot_template/save', JSON.stringify(this.setting)).then(response => {
          $('#loading').css({display: 'none'})
        })
      }
    },
    startDrag: (event, index) => {
      event.dataTransfer.dropEffect = 'move'
      event.dataTransfer.effectAllowed = 'move'
      event.dataTransfer.setData('menuIndex', index)
    },
    onDrop(evt, id) {
      const index = evt.dataTransfer.getData('menuIndex')
      let temp = this.setting.menus[index]
      this.setting.menus.splice(index, 1)
      this.setting.menus.splice(id, 0, temp)
    }
  }
}
</script>
<style scoped>
fa {
  margin-left: 75px;
  cursor: pointer;
  user-select: none;
}
.card-header {
  max-height: 1.9cm;
}
#btn {
  width: 105px;
}
@media (max-width: 992px) {
  .container-fluid{
    padding-left: 0px;
    padding-right: 0px;
    }
}
</style>
