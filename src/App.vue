<template>
  <v-app>
    <v-app-bar app color="indigo">
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title style="color: white">Онлайн-прачечная</v-toolbar-title>
      <v-menu v-model="translate" offset-x rounded="b-xl" :close-on-content-click="false" nudge-width="120">
        <template v-slot:activator="{on, attrs}">
          <v-btn v-on="on" v-bind="attrs" color="white" icon title="Выбор языка">
            <v-icon>translate</v-icon>
          </v-btn>
        </template>
        <v-card>
          <v-radio-group style="margin: 5%">
            <v-radio
                label="Украинский"
            ></v-radio>
            <v-radio
                label="Русский"
            ></v-radio>
          </v-radio-group>
        </v-card>
      </v-menu>
      <v-spacer></v-spacer>
      <v-spacer></v-spacer>
      <v-menu offset-y v-if="isAuth" v-model="info.userInfo">
        <template v-slot:activator="{on, attrs}">
          <v-avatar v-on="on" v-bind="attrs">
            <v-img :src="info.userInfo[0].avatar"></v-img>
          </v-avatar>
        </template>
        <v-card>
          <v-card-subtitle>{{info.userInfo[0].f_name}} {{info.userInfo[0].s_name}}</v-card-subtitle>
          <v-divider></v-divider>
          <v-list flat>
            <v-list-item-group>
              <v-list-item @click="$router.push('/cabinet')">
                <v-list-item-icon><v-icon>person</v-icon></v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>Личный кабинет</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item @click="$router.push('/')">
                <v-list-item-icon><v-icon>apps</v-icon></v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>Каталог</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-divider></v-divider>
              <v-list-item @click="$router.push('/logout')">
                <v-list-item-icon><v-icon>exit_to_app</v-icon></v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>Выйти</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list-item-group>
          </v-list>
        </v-card>
      </v-menu>
      <v-menu offset-y v-else-if="!isAuth">
        <template v-slot:activator="{on, attrs}">
          <v-avatar v-on="on" v-bind="attrs" color="primary" title="Anonymous">A</v-avatar>
        </template>
        <v-card>
          <v-list>
            <v-list-item-group>
              <v-list-item>
                <v-list-item-icon><v-icon>attach_money</v-icon></v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>{{info.userInfo.balance}} UAH</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item @click="$router.push('/')">
                <v-list-item-icon><v-icon>apps</v-icon></v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>Каталог</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item @click="register = true">
                <v-list-item-icon><v-icon>person_add</v-icon></v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>Регистрация</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item @click="auth = true">
                <v-list-item-icon><v-icon>login</v-icon></v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>Авторизация</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list-item-group>
          </v-list>
        </v-card>
      </v-menu>
    </v-app-bar>
    <v-dialog v-model="auth" max-width="600px" persistent>
      <v-card>
        <v-card-title>
          Авторизация
          <v-spacer></v-spacer>
          <v-btn icon @click="auth = false">
            <v-icon>
              close
            </v-icon>
          </v-btn>
        </v-card-title>
        <v-divider></v-divider>
        <v-alert
            v-model="errForm"
            color="red"
            dense
            outlined
            type="error"
        >
          {{errText}}
        </v-alert>
        <v-form>
          <v-container>
            <v-row>
              <v-col>
                <v-text-field
                    label="Логин*"
                    v-model="login"
                    :rules="textRules"
                    required
                ></v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <v-text-field
                    type="password"
                    label="Пароль*"
                    v-model="pwd"
                    :rules="textRules"
                    required
                ></v-text-field>
              </v-col>
            </v-row>
          </v-container>
          <v-btn @click="doAuth" color="success" width="100%">ВОЙТИ</v-btn>
        </v-form>
      </v-card>
    </v-dialog>
    <router-view></router-view>
    <v-navigation-drawer app v-model="drawer" color="indigo">

    </v-navigation-drawer>
  </v-app>
</template>

<script>
  const ip = "192.168.0.113"
  const port = "9000"
  const axios = require('axios')
  export default {
    name: 'App',
    data() {
      return {
        info: {
          userInfo: null
        },
        drawer: false,
        isAuth: false,
        auth: false,
        register: false,
        errForm: false,
        errText: null,
        translate: false,
        login: '',
        pwd: '',
        textRules: [
          v => !!v || 'Это поле обязательно',
          v => v.length !== 0 || "Поле не может быть пусто"
        ]
      }
    },
    mounted() {
      axios.get(`http://${ip}:${port}/api/persons/35`)
          .then(resp => {
            this.info.userInfo = resp.data
            if (this.info.userInfo.balance === null || this.info.userInfo.balance === 0) {
              this.info.userInfo.balance =  "00, 00"
            } else {
              this.info.userInfo.balance = this.info.userInfo.balance.toString() + ", 00";
            }
          })
      // let req = new XMLHttpRequest()
      let info;
      // req.open('GET', `http://${ip}:${port}/api/persons`, false)
      // req.send()
      // info = JSON.parse(req.responseText)['users'].filter(i => i.session_id === +localStorage.getItem('uid'))
      // this.info.userInfo = info
      this.isAuth = info.length > 0
    },
    methods: {
      getUserId() {
        let request = new XMLHttpRequest();
        let info;
        request.open('GET', `http://${ip}:${port}/api/persons`, false)
        request.send()
        info = JSON.parse(request.responseText).filter(i => i.login === this.login)
        return info
      },
      doAuth() {
        let req = new XMLHttpRequest()
        req.open('GET', `http://${ip}:${port}/api/persons`, false)
        req.send()
        let info = JSON.parse(req.responseText).filter(i => i.login === this.login && i.pwd === this.pwd)
        if (info.length > 0) {
          this.errForm = false
          let sId = ''
          for (let i=0;i<10;i++) {
            sId += Math.floor(Math.random()*10)
          }
          let userId = this.getUserId()[0].id;
          console.log(userId)
          axios.put(`http://${ip}:${port}/api/person/`+userId, {
            session_id: sId
          })
              .then(resp => {
                console.log(resp)
                localStorage.setItem('uid', sId)
              })
              .catch(err => console.log('catch error', err))
          this.login = ''
          this.pwd = ''
          this.auth = false
          this.info.userInfo = info
        } else {
          this.errForm = true
          this.errText = 'Введены неверные данные'
        }
      }
    }
  }
</script>

<style lang="scss">
</style>
