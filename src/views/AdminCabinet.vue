<template>
  <v-app style="z-index: 5">
    <v-card style="margin: 5% 15% 0 15%">
      <v-tabs grow color="indigo">
        <v-tab>Администрирование</v-tab>
        <v-tab>Отчеты</v-tab>
        <v-tab>Статистика</v-tab>
        <v-tab-item>
          <v-text-field
              style="margin: 2%"
              full-width
              label="Введите запрос"
              append-icon="search"
              outlined
          ></v-text-field>
          <v-card-title>Управление данными</v-card-title>
          <v-divider></v-divider>
<!--          style="display: grid; grid-gap: 4px; grid-template-columns: repeat(4, auto);"-->
          <v-container>
            <v-row>
              <v-col cols="4" v-for="(item, i) in tablesInfo.tablesName" :key="i">
                <v-card max-width="200" style="margin: 2%" :name="item">
                  <v-card-subtitle>{{ item }}</v-card-subtitle>
                  <v-card-text>
                    <b>Кол-во записей:</b> <br/> {{tablesInfo.countsRows[i]}} <br/>
                    <b>Дата изменения:</b> <br/> {{tablesInfo.datesEdits[i]}}
                  </v-card-text>
                  <br>
                  <br>
                  <v-btn color="indigo" outlined width="100%" @click="handleTable">Подробнее</v-btn>
                </v-card>
              </v-col>
            </v-row>
          </v-container>
          <v-dialog max-width="650" persistent v-model="fullInfo" v-if="chosenTable !== null">
            <v-card>
              <v-card-title>
                Управление {{ chosenTable }}
                <v-spacer></v-spacer>
                <v-btn icon @click="fullInfo = false">
                  <v-icon>
                    close
                  </v-icon>
                </v-btn>
              </v-card-title>
              <v-text-field
                  style="margin: 2%"
                  full-width
                  :label="'Введите запрос к ' + chosenTable"
                  append-icon="search"
                  outlined
              ></v-text-field>
              <v-snackbar top text timeout="2000" v-model="alertSuccess" color="indigo">
                {{alertText}}
              </v-snackbar>
              <v-divider></v-divider>
              <v-card style="justify-content: center; display: flex; margin: 2%" flat tile>
                <v-hover v-slot:default="{hover}">
                  <v-btn outlined elevation="3" color="indigo" @click="showAddForm = true">
                    <span v-if="hover">Добавить</span>
                    <v-icon>
                      add
                    </v-icon>
                  </v-btn>
                </v-hover>
                <v-hover v-slot:default="{hover}">
                  <v-btn outlined elevation="3" color="indigo" @click="modeEdit = !modeEdit">
                    <span v-if="hover">Изменить</span>
                    <v-icon>
                      edit
                    </v-icon>
                  </v-btn>
                </v-hover>
                <v-hover v-slot:default="{hover}">
                  <v-btn outlined elevation="3" color="indigo" @click="modeDel = !modeDel">
                    <span v-if="hover">Удалить</span>
                    <v-icon>
                      delete
                    </v-icon>
                  </v-btn>
                </v-hover>
              </v-card>
              <v-divider></v-divider>
              <PersonsList :updater="updater" :show-edit="showEditForm" :show-del="showDelForm" :item-info="forms.persons" :table-info="chosenTableInfo" :mode-del="modeDel" :mode-edit="modeEdit" v-if="chosenTable === 'Persons'"/>
            </v-card>
          </v-dialog>
          <v-dialog max-width="1000" persistent v-model="showAddForm">
            <v-card>
              <v-card-title>
                Добавление записи в таблицу {{chosenTable}}
                <v-spacer></v-spacer>
                <v-btn icon @click="showAddForm = false">
                  <v-icon>
                    close
                  </v-icon>
                </v-btn>
              </v-card-title>
              <v-divider></v-divider>
              <v-tabs grow color="indigo">
                <v-tab>Обязательные поля</v-tab>
                <v-tab>Необязательные поля</v-tab>
                <v-tab-item>
                  <v-container>
                    <v-row>
                      <v-col>
                        <v-row>
                          <v-col>
                            <v-text-field
                                label="Имя"
                                outlined
                                :rules="rulesText"
                                required
                                v-model="forms.persons.fName"
                            ></v-text-field>
                            <v-text-field
                                label="Фамилия"
                                outlined
                                :rules="rulesText"
                                required
                                v-model="forms.persons.sName"
                            ></v-text-field>
                            <v-text-field
                                label="Логин"
                                outlined
                                :rules="rulesText"
                                required
                                v-model="forms.persons.login"
                            ></v-text-field>
                          </v-col>
                          <v-col>
                            <v-text-field
                                type="password"
                                label="Пароль"
                                outlined
                                :rules="rulesText"
                                required
                                v-model="forms.persons.pwd"
                            ></v-text-field>
                            <v-text-field
                                label="E-mail"
                                outlined
                                :rules="emailRules"
                                required
                                v-model="forms.persons.email"
                            ></v-text-field>
                            <v-text-field
                                label="Телефон"
                                outlined
                                :rules="phoneRules"
                                required
                                v-model="forms.persons.phone"
                            ></v-text-field>
                          </v-col>
                        </v-row>
                      </v-col>
                    </v-row>
                  </v-container>
                </v-tab-item>
                <v-tab-item>
                  <v-container>
                    <v-row>
                      <v-col>
                        <v-card-subtitle>Доступные машини:</v-card-subtitle>
                        <div v-if="info.machines.length > 0 ">
                          <v-checkbox v-for="(item, i) in info.machines" :label="item.name + ' ' + item.capacity" :key="i"></v-checkbox>
                        </div>
                        <v-checkbox readonly label="Машини отсутствуют"></v-checkbox>
                      </v-col>
                    </v-row>
                  </v-container>
                </v-tab-item>
              </v-tabs>
              <v-divider></v-divider>
              <v-btn @click="doAdd" outlined color="indigo" width="100%">Создать</v-btn>
            </v-card>
          </v-dialog>
          <v-dialog max-width="1000" persistent v-model="showEditForm">
            <v-card>
              <v-card-title>
                Изменение записи в таблице {{chosenTable}}
                <v-spacer></v-spacer>
                <v-btn icon @click="modeEdit = false">
                  <v-icon>
                    close
                  </v-icon>
                </v-btn>
              </v-card-title>
              <v-divider></v-divider>
              <v-tabs grow color="indigo">
                <v-tab>Обязательные поля</v-tab>
                <v-tab>Необязательные поля</v-tab>
                <v-tab-item>
                  <v-container>
                    <v-row>
                      <v-col>
                        <v-img width="200" height="185" :src="forms.persons.avatar"></v-img>
                        <v-file-input
                            full-width
                            label="Аватар пользователя"
                            append-icon="mdi-camera"
                            v-model="forms.persons.avatar"
                            @change="loadImg"
                            outlined
                        ></v-file-input>
                      </v-col>
                      <v-col>
                        <v-row>
                          <v-col>
                            <v-text-field
                                label="Имя"
                                :placeholder="info.persons.fName"
                                outlined
                                :rules="rulesText"
                                v-model="forms.persons.fName"
                            ></v-text-field>
                            <v-text-field
                                label="Фамилия"
                                :placeholder="info.persons.sName"
                                outlined
                                :rules="rulesText"
                                v-model="forms.persons.sName"
                            ></v-text-field>
                            <v-text-field
                                label="Логин"
                                :placeholder="info.persons.login"
                                outlined
                                :rules="rulesText"
                                v-model="forms.persons.login"
                            ></v-text-field>
                          </v-col>
                          <v-col>
                            <v-text-field
                                type="password"
                                label="Пароль"
                                :placeholder="info.persons.pwd"
                                outlined
                                :rules="rulesText"
                                v-model="forms.persons.pwd"
                            ></v-text-field>
                            <v-text-field
                                label="E-mail"
                                :placeholder="info.persons.email"
                                outlined
                                :rules="emailRules"
                                v-model="forms.persons.email"
                            ></v-text-field>
                            <v-text-field
                                label="Телефон"
                                :placeholder="info.persons.phone"
                                outlined
                                :rules="phoneRules"
                                v-model="forms.persons.phone"
                            ></v-text-field>
                          </v-col>
                        </v-row>
                      </v-col>
                    </v-row>
                  </v-container>
                </v-tab-item>
                <v-tab-item>
                  <v-container>
                    <v-row>
                      <v-col>
                        <v-card-subtitle>Доступные машини:</v-card-subtitle>
                        <div v-if="forms.persons.machine.length > 0 ">
                          <v-checkbox v-for="(item, i) in forms.persons.machine" :label="item.name + ' ' + item.capacity" :key="i"></v-checkbox>
                        </div>
                        <v-checkbox readonly label="Машини отсутствуют"></v-checkbox>
                      </v-col>
                    </v-row>
                  </v-container>
                </v-tab-item>
              </v-tabs>
              <v-divider></v-divider>
              <v-btn @click="doEdit" outlined color="indigo" width="100%">Изменить</v-btn>
            </v-card>
          </v-dialog>
          <v-dialog max-width="400" persistent v-model="showDelForm">
            <v-card>
              <v-card-title>
                Сейчас будет удалена запись из <br/> {{chosenTable}}. <br/> Продолжить ?
              </v-card-title>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn @click="modeDel = false" text color="green">Нет, отмена.</v-btn>
                <v-btn @click="doRemove" text color="red">Да, удалить.</v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-tab-item>
        <v-tab-item></v-tab-item>
        <v-tab-item></v-tab-item>
      </v-tabs>
    </v-card>
  </v-app>
</template>

<script>
import PersonsList from "@/components/PersonsList";
const ip = "192.168.0.113"
const port = '9000'
const axios = require('axios')

export default {
  name: "AdminCabinet",
  components: {PersonsList},
  data() {
    return {
      tablesInfo: {
        tablesName: [
          'Persons', 'Machines', 'Stocks', 'Drafts', 'Wallets', 'HistoryTransactions'
        ],
        countsRows: [],
        datesEdits: []
      },
      chosenTableInfo: null,
      chosenTable: null,
      selectedItem: null,
      fullInfo: false,
      showAddForm: false,
      showEditForm: false,
      showDelForm: false,
      modeEdit: false,
      modeDel: false,
      info: {
        machines: [],
        persons: [],
        wallets: [],
        histories: [],
        stocks: []
      },
      rulesText: [
          v => !!v || 'Это поле не может быть пустым',
          v => v.length > 0 || 'Это поле не может быть пустым'
      ],
      phoneRules: [
        v => v.length > 0 || 'Это поле не может быть пустым',
        v => v.length === 10 || 'Введите коректный номер'
      ],
      emailRules: [
        v => v.length > 0 || 'Это поле не может быть пустым',
        v => v.match("[a-zA-Z]+@[a-zA-Z]+[.][a-zA-Z]+") !== null || 'Введите коректный e-mail'
      ],
      alertSuccess: false,
      alertErr: false,
      alertText: '',
      forms: {
        persons: {
          fName: '',
          sName: '',
          login: '',
          email: '',
          pwd: '',
          phone: '',
          avatar: '',
          machine: ''
        },
        machines: {
          stock: null,
          name: null,
          capacity: null,
          description: null,
          status: null,
          price: null,
          img: null
        },
        drafts: {
          person: null,
          machine: null,
          price: null,
          volume: null,
          additional: null,
          paymentType: null,
          creditCard: null
        },
        wallets: {
          balance: null
        },
        histories: {
          sum: null,
          date: null
        },
        stocks: {
          name: null,
          sponsor: null,
          lastTerm: null,
          discount: null
        }
      }
    }
  },
  methods: {
    updater(info) {
      this.forms[this.chosenTable.toLowerCase()] = info.infoItem
        if (info.showEdit) this.showEditForm = true
        else if (info.showDel) this.showDelForm = true
    },
    loadImg(ev) {
      let reader = new FileReader()
      reader.onload = (e) => {
        this.forms[this.chosenTable.toLowerCase()].avatar = e.target.result
      }
      reader.readAsDataURL(ev);
    },
    doAdd() {
      try {
        let table = this.chosenTable
        let curForms = this.forms[table.toLowerCase()]
        console.log('info', curForms)
        if (curForms.machine === '') curForms.machine = []
        axios({
          method: "POST",
          url: `http://${ip}:${port}/api/${table.toLowerCase()}`,
          data: this.forms[table.toLowerCase()]
        }).then(resp => {
          console.log(resp)
          this.info[table.toLowerCase()].push(resp.data)
        })
        console.log('info', curForms)
        for (let item of Object.keys(this.forms[table.toLowerCase()])) {
          this.forms[table.toLowerCase()][item] = '';
        }
        this.fullInfo = false;
        this.$nextTick(() => {
          this.showAddForm = false;
          this.alertSuccess = true;
          this.alertText = 'Успешно добавлена запись';
          this.fullInfo = true;
        })
        this.forms.persons = null;
        this.selectedItem = ''
      } catch (e) {
        console.log('err', e)
        this.$nextTick(() => {
          this.alertErr = true;
          this.alertText = 'Ошибка сервера';
        })
      }
    },
    doEdit() {
      try {
        let curItem = this.forms[table.toLowerCase()].id
        let table = this.chosenTable
        axios({
          method: "PUT",
          url: `http://${ip}:${port}/api/${table.toLowerCase()}/${curItem}`,
          data: this.forms[table.toLowerCase()]
        }).then(resp => {
          console.log(resp)
          this.info[table.toLowerCase()] = this.info[table.toLowerCase()].filter(i => i.id !== resp.data.id)
          this.info[table.toLowerCase()].push(resp.data)
        })
        for (let item of Object.keys(this.forms[table.toLowerCase()])) {
          this.forms[table.toLowerCase()][item] = '';
        }
        this.fullInfo = false;
        this.$nextTick(() => {
          this.showEditForm = false;
          this.alertSuccess = true;
          this.alertText = 'Запись успешно обновлена'
          this.fullInfo = true;
        })
        this.forms.persons = null;
      } catch (e) {
        console.log('err', e)
        this.$nextTick(() => {
          this.alertErr = true;
        })
      }
    },
    doRemove() {
      try {
        let curItem = this.forms[table.toLowerCase()].id
        let table = this.chosenTable
        axios({
          method: "DELETE",
          url: `http://${ip}:${port}/api/${table.toLowerCase()}/${curItem}`
        }).then(resp => console.log(resp))
        this.fullInfo = false
        for (let item of Object.keys(this.forms[table.toLowerCase()])) {
          this.forms[table.toLowerCase()][item] = '';
        }
        this.$nextTick(() => {
          this.info[table.toLowerCase()] = this.info[table.toLowerCase()].filter(i => i.id !== curItem)
          this.showDelForm = false;
          this.alertSuccess = true;
          this.alertText = 'Запись успешно удалена'
          this.fullInfo = true;
        })
      } catch (e) {
        console.log('err', e)
        this.$nextTick(() => {
          this.alertErr = true;
        })
      }
    },
    handleItemEdit() {
      let curItem = this.selectedItem.split(':')[1].trim();
      let table = this.chosenTable
      axios({
        method: "GET",
        url: `http://${ip}:${port}/api/${table.toLowerCase()}/${curItem}`,
      }).then(resp => (this.forms.persons = resp.data))
    },
    loadingData() {
      let curTable = this.chosenTable;
      this.mappedItems = this.chosenTableInfo.map(i => 'ID: '+i.id);
      console.log('mapped', this.mappedItems)
      console.log('default', this.chosenTableInfo)
      axios.get(`http://${ip}:${port}/api/${curTable.toLowerCase()}/${this.selectedItem.split(':')[1]}`)
          .then(resp => {
            this.info.persons = resp.data
            console.log(resp.data)
          })
    },
    handleTable(ev) {
      this.fullInfo = true;
      if (this.chosenTable === null) {
        this.chosenTable = ev.path[0].offsetParent.offsetParent.attributes.getNamedItem('name').value || ev.path[0].offsetParent.attributes.getNamedItem('name').value
      }
      console.log(this.chosenTable)
      axios({
        method: 'GET',
        url: `http://${ip}:${port}/api/${this.chosenTable.toLowerCase()}`,
      }).then(resp => (this.chosenTableInfo = resp.data))
    }
  },
  mounted() {
    axios.get(`http://${ip}:${port}/api/persons`)
      .then(resp => {
        if (resp.data.length > 0) {
          this.tablesInfo.countsRows.push(resp.data.length)
        } else {
          this.tablesInfo.countsRows.push(0)
        }
        this.info.persons = resp.data
        this.tablesInfo.datesEdits.push(resp.data[resp.data.length-1].lastUpdateRow)
      })
    axios.get(`http://${ip}:${port}/api/machines`)
        .then(resp => {
          if (resp.data.length > 0) {
            this.tablesInfo.countsRows.push(resp.data.length)
            this.info.machines = resp.data
            this.tablesInfo.datesEdits.push(resp.data[resp.data.length-1].lastUpdateRow)
          } else {
            this.tablesInfo.countsRows.push(0)
            this.tablesInfo.datesEdits.push('Отсутствует')
          }
        })
    axios.get(`http://${ip}:${port}/api/drafts`)
        .then(resp => {
          if (resp.data.length > 0) {
            this.tablesInfo.countsRows.push(resp.data.length)
            this.tablesInfo.datesEdits.push(resp.data[resp.data.length-1].lastUpdateRow)
          } else {
            this.tablesInfo.countsRows.push(0)
            this.tablesInfo.datesEdits.push('Отсутствует')
          }
        })
    axios.get(`http://${ip}:${port}/api/stocks`)
        .then(resp => {
          if (resp.data.length > 0) {
            this.tablesInfo.countsRows.push(resp.data.length)
            this.info.stocks = resp.data
            this.tablesInfo.datesEdits.push(resp.data[resp.data.length-1].lastUpdateRow)
          } else {
            this.tablesInfo.countsRows.push(0)
            this.tablesInfo.datesEdits.push('Отсутствует')
          }
        })
    axios.get(`http://${ip}:${port}/api/wallets`)
        .then(resp => {
          if (resp.data.length > 0) {
            this.tablesInfo.countsRows.push(resp.data.length)
            this.info.wallets = resp.data
            this.tablesInfo.datesEdits.push(resp.data[resp.data.length-1].lastUpdateRow)
          } else {
            this.tablesInfo.countsRows.push(0)
            this.tablesInfo.datesEdits.push('Отсутствует')
          }
        })
    axios.get(`http://${ip}:${port}/api/histories`)
        .then(resp => {
          if (resp.data.length > 0) {
            this.tablesInfo.countsRows.push(resp.data.length)
            this.info.histories = resp.data
            this.tablesInfo.datesEdits.push(resp.data[resp.data.length-1].lastUpdateRow)
          } else {
            this.tablesInfo.countsRows.push(0)
            this.tablesInfo.datesEdits.push('Отсутствует')
          }
        })
  }
}
</script>

<style scoped>

</style>