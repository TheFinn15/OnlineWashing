<template>
  <v-app>
    <v-app-bar app color="indigo">
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title style="color: white">{{curLocale.toolbarTitle}}</v-toolbar-title>
      <v-menu v-model="translate" offset-x rounded="b-xl" :close-on-content-click="false" nudge-width="140">
        <template v-slot:activator="{on, attrs}">
          <v-btn v-on="on" v-bind="attrs" color="white" icon :title="curLocale.locales.tip">
            <v-icon>translate</v-icon>
          </v-btn>
        </template>
        <v-card>
          <v-card-subtitle>{{curLocale.locales.langSubTitle}}</v-card-subtitle>
          <v-divider></v-divider>
          <v-list shaped>
            <v-list-item-group color="indigo">
              <v-list-item @click="changeLangEN">
                <v-list-item-content>
                  <v-list-item-title>
                    {{curLocale.locales.selects[0]}}
                  </v-list-item-title>
                </v-list-item-content>
                <v-list-item-icon>
                  <v-icon>chevron_right</v-icon>
                </v-list-item-icon>
              </v-list-item>
              <v-list-item @click.prevent="changeLangRU">
                <v-list-item-content>
                  <v-list-item-title>
                    {{curLocale.locales.selects[1]}}
                  </v-list-item-title>
                </v-list-item-content>
                <v-list-item-icon>
                  <v-icon>chevron_right</v-icon>
                </v-list-item-icon>
              </v-list-item>
              <v-list-item @click="changeLangUA">
                <v-list-item-content v-model="langThird">
                  <v-list-item-title>
                    {{curLocale.locales.selects[2]}}
                  </v-list-item-title>
                </v-list-item-content>
                <v-list-item-icon>
                  <v-icon>chevron_right</v-icon>
                </v-list-item-icon>
              </v-list-item>
            </v-list-item-group>
          </v-list>
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
              <v-list-item @click="$router.push('/balance')">
                <v-list-item-icon><v-icon>attach_money</v-icon></v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>{{info.userInfo}} {{curLocale.authedUser.menuItems[0]}}</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item @click="$router.push('/cabinet')">
                <v-list-item-icon><v-icon>person</v-icon></v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>{{curLocale.authedUser.menuItems[1]}}</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item @click="$router.push('/')">
                <v-list-item-icon><v-icon>apps</v-icon></v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>{{curLocale.authedUser.menuItems[2]}}</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-divider></v-divider>
              <v-list-item @click="$router.push('/logout')">
                <v-list-item-icon><v-icon>exit_to_app</v-icon></v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>{{curLocale.authedUser.menuItems[3]}}</v-list-item-title>
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
              <v-list-item @click="$router.push('/balance')">
                <v-list-item-icon><v-icon>attach_money</v-icon></v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>{{info.userInfo}} {{curLocale.nonAuthedUser.menuItems[0]}}</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item @click="$router.push('/')">
                <v-list-item-icon><v-icon>apps</v-icon></v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>{{curLocale.nonAuthedUser.menuItems[1]}}</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item @click="register = true">
                <v-list-item-icon><v-icon>person_add</v-icon></v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>{{curLocale.nonAuthedUser.menuItems[2]}}</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item @click="auth = true">
                <v-list-item-icon><v-icon>login</v-icon></v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>{{curLocale.nonAuthedUser.menuItems[3]}}</v-list-item-title>
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
          {{curLocale.authForm.title}}
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
                    :label="curLocale.authForm.labels[0]"
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
                    :label="curLocale.authForm.labels[1]"
                    v-model="pwd"
                    :rules="textRules"
                    required
                ></v-text-field>
              </v-col>
            </v-row>
          </v-container>
          <v-btn @click="doAuth" color="success" width="100%">{{curLocale.authForm.btnTitle}}</v-btn>
        </v-form>
      </v-card>
    </v-dialog>
    <router-view></router-view>
    <v-navigation-drawer app v-model="drawer" color="indigo" v-if="$route.fullPath !== ('/admin/cabinet' || '/admin/auth')">

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
        curLocale: null,
        locales: {
          'en-EN': {
            toolbarTitle: 'Online Washing',
            locales: {
              langSubTitle: 'Language of Interface',
              tip: 'Choice of language',
              selects: [
                'English', 'Russian', 'Ukrainian'
              ]
            },
            authedUser: {
              menuItems: [
                'UAH', 'Personal Area', 'Catalog', 'Logout'
              ]
            },
            nonAuthedUser: {
              menuItems: [
                'UAH', 'Catalog', 'Sign-Up', 'Sign-In'
              ]
            },
            authForm: {
              title: 'Sign-In',
              labels: [
                'Login*', 'Password*'
              ],
              rulesText: [
                'This field can not be a empty'
              ],
              btnTitle: 'ENTER'
            }
          },
          'ru-RU': {
            toolbarTitle: 'Онлайн-прачечная',
            tip: 'Выбор языка',
            locales: {
              langSubTitle: 'Язык интерфейса',
              selects: [
                  'English', 'Русский', 'Украинский'
              ]
            },
            authedUser: {
              menuItems: [
                  'ГРН', 'Личный кабинет', 'Каталог', 'Выйти'
              ]
            },
            nonAuthedUser: {
              menuItems: [
                'ГРН', 'Каталог', 'Регистрация', 'Авторизация'
              ]
            },
            authForm: {
              title: 'Авторизация',
              labels: [
                  'Логин*', 'Пароль*'
              ],
              rulesText: [
                  'Это поле не может быть пустым'
              ],
              btnTitle: 'ВОЙТИ'
            }
          },
          'ua-UA': {
            toolbarTitle: 'Онлайн-пральня',
            locales: {
              langSubTitle: 'Мова інтерфейсу',
              tip: 'Вибір мови',
              selects: [
                'English', 'Російська', 'Українська'
              ]
            },
            authedUser: {
              menuItems: [
                'ГРН', 'Особистий кабінет', 'Каталог', 'Вийти'
              ]
            },
            nonAuthedUser: {
              menuItems: [
                'ГРН', 'Каталог', 'Реєстрація', 'Авторизація'
              ]
            },
            authForm: {
              title: 'Авторизація',
              labels: [
                'Логін*', 'Пароль*'
              ],
              rulesText: [
                'Поле не може бути порожнім'
              ],
              btnTitle: 'ВІЙТИ'
            }
          }
        },
        register: false,
        errForm: false,
        errText: null,
        translate: false,
        langOne: false,
        langSecond: false,
        langThird: false,
        login: '',
        pwd: '',
        textRules: [
          v => v.length !== 0 || this.curLocale.authForm.rulesText[0]
        ]
      }
    },
    mounted() {
      axios.get(`http://${ip}:${port}/api/persons/1`)
          .then(resp => {
            this.info.userInfo = resp.data
            if (this.info.userInfo.wallet.balance === null || this.info.userInfo.wallet.balance === 0 || this.info.userInfo.wallet.balance === undefined) {
              this.info.userInfo.wallet.balance =  "00, 00"
            } else {
              this.info.userInfo.wallet.balance = this.info.userInfo.wallet.balance.toString() + ", 00";
            }
          })
      if (localStorage.getItem('lang') === null) {
        localStorage.setItem('lang', 'ua-UA')
        this.langThird = true;
        this.langOne = false;
        this.langSecond = false;
      }
      // let req = new XMLHttpRequest()
      // let info;
      // req.open('GET', `http://${ip}:${port}/api/persons`, false)
      // req.send()
      // info = JSON.parse(req.responseText)['users'].filter(i => i.session_id === +localStorage.getItem('uid'))
      // this.info.userInfo = info
      // this.isAuth = info.length > 0
    },
    beforeMount() {
      if (localStorage['lang'] === 'ru-RU') {
        this.curLocale = this.locales["ru-RU"];
      } else if (localStorage['lang'] === 'en-EN') {
        this.curLocale = this.locales["en-EN"];
      } else if (localStorage['lang'] === 'ua-UA') {
        this.curLocale = this.locales["ua-UA"];
      } else {
        localStorage.setItem('lang', 'ua-UA')
        this.curLocale = this.locales["ua-UA"];
      }
    },
    methods: {
      changeLangEN() {
        localStorage.setItem('lang', 'en-EN')
        this.curLocale = this.locales["en-EN"];
        this.$router.go('/cabinet');
      },
      changeLangRU() {
        localStorage.setItem('lang', 'ru-RU')
        this.curLocale = this.locales["ru-RU"];
        this.$router.go('/cabinet');
      },
      changeLangUA() {
        localStorage.setItem('lang', 'ua-UA')
        this.curLocale = this.locales["ua-UA"];
        this.$router.go('/cabinet');
      },
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
