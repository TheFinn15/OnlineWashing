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
          <div style="display: grid; grid-gap: 4px; grid-template-columns: repeat(4, auto);">
            <v-card max-width="200" v-for="(item, i) in tablesInfo.tablesName" :key="i" style="margin: 4%" :name="item">
              <v-card-subtitle>{{ item }}</v-card-subtitle>
              <v-card-text>
                <b>Дата изменения:</b> {{tablesInfo.datesEdits[i]}} <br/>
                <b>Кол-во записей:</b> {{tablesInfo.countsRows[i]}} <br/>
              </v-card-text>
              <br>
              <br>
              <v-btn color="indigo" outlined width="100%" @click="handleTable">Подробнее</v-btn>
            </v-card>
          </div>
          <v-dialog max-width="1000" persistent v-model="fullInfo">
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
              <v-tabs grow color="indigo">
                <v-tab>Редактор</v-tab>
                <v-tab>Просмотр</v-tab>
                <v-tab-item>
                  <v-container>
                    <v-row>
                      <v-col>
                        <v-select outlined :items="actionTable" label="Действия" v-model="activeAction"></v-select>
                        <v-row v-if="activeAction === 'Создать'">
                          <v-col>
                            <v-img width="200" height="185" :src="avatar"></v-img>
                            <v-file-input
                                full-width
                                label="Аватар пользователя"
                                append-icon="mdi-camera"
                                outlined
                            ></v-file-input>
                          </v-col>
                          <v-col>
                            <v-row>
                              <v-col>
                                <v-text-field
                                    label="Имя"
                                    outlined
                                    :rules="rulesText"
                                    required
                                    v-model="fName"
                                ></v-text-field>
                                <v-text-field
                                    label="Фамилия"
                                    outlined
                                    :rules="rulesText"
                                    required
                                    v-model="sName"
                                ></v-text-field>
                                <v-text-field
                                    label="Логин"
                                    outlined
                                    :rules="rulesText"
                                    required
                                    v-model="login"
                                ></v-text-field>
                              </v-col>
                              <v-col>
                                <v-text-field
                                    label="Пароль"
                                    outlined
                                    :rules="rulesText"
                                    required
                                    v-model="pwd"
                                ></v-text-field>
                                <v-text-field
                                    label="E-mail"
                                    outlined
                                    :rules="emailText"
                                    required
                                    v-model="email"
                                ></v-text-field>
                                <v-text-field
                                    label="Телефон"
                                    outlined
                                    :rules="rulesText"
                                    required
                                    v-model="phone"
                                ></v-text-field>
                              </v-col>
                            </v-row>
                          </v-col>
                        </v-row>
                      </v-col>
                      <v-col>
                        <v-select outlined :items="getMappedItems" label="Записи" v-model="selectedItem" v-if="activeAction === 'Редактировать' || activeAction === 'Удалить'"></v-select>
                        <v-card-title v-if="activeAction === 'Создать' || activeAction === 'Редактировать' || activeAction === 'Удалить'">Необязательные поля</v-card-title>
                        <v-row v-if="activeAction === 'Создать' || activeAction === 'Редактировать' || activeAction === 'Удалить'">
                          <v-col v-if="info.machines !== null">
                            <v-card-subtitle>Стиральные машини:</v-card-subtitle>
                            <v-checkbox dense v-for="(item, i) in info.machines" :key="i" :label="item.name + item.capacity"></v-checkbox>
                          </v-col>
                          <v-col v-else>
                            <v-card-subtitle>
                              Стиральные машини: <br/>
                              Отсутствуют
                            </v-card-subtitle>
                          </v-col>
                          <v-col></v-col>
                        </v-row>
                      </v-col>
                    </v-row>
                  </v-container>
                </v-tab-item>
                <v-tab-item>
                  <v-list flat v-if="chosenTable === 'Persons'">
                    <v-list-group v-for="(item, i) in chosenTableInfo" :key="i" color="indigo">
                      <template v-slot:activator>
                        <v-list-item-title>
                          ID: {{item.id}} | {{item.fName}} {{item.sName}}
                        </v-list-item-title>
                      </template>
                      <!--Информация о юзере-->
                      <v-list-group sub-group no-action color="indigo">
                        <template v-slot:activator>
                          <v-list-item-content>
                            <v-list-item-title>Информация о пользователе</v-list-item-title>
                          </v-list-item-content>
                        </template>
                          <v-list-item>
                            <v-list-item-title>Логин: {{item.login}}</v-list-item-title>
                          </v-list-item>
                          <v-list-item>
                            <v-list-item-title>Пароль: {{item.pwd}}</v-list-item-title>
                          </v-list-item>
                          <v-list-item>
                            <v-list-item-title>Е-маил: {{item.email}}</v-list-item-title>
                          </v-list-item>
                          <v-list-item>
                            <v-list-item-title>Телефон: {{item.phone}}</v-list-item-title>
                          </v-list-item>
                          <v-list-item>
                            <v-list-item-title>Дата обновления записи: {{item.lastUpdateRow}}</v-list-item-title>
                          </v-list-item>
                      </v-list-group>
                      <!---------------------------------------------------------------------------------->
                      <!--Отображение баланса-->
                      <v-list-group sub-group no-action>
                        <template v-slot:activator>
                          <v-list-item-content>
                            <v-list-item-title>ID: {{item.wallet.id}} | {{item.wallet.balance}} UAH</v-list-item-title>
                          </v-list-item-content>
                        </template>
                        <v-list-group sub-group no-action v-if="item.wallet.historyTransactions.length > 0">
                          <template v-slot:activator>
                            <v-list-item-content>
                              <v-list-item-title v-if="item.wallet.historyTransactions !== null">История пополнений</v-list-item-title>
                            </v-list-item-content>
                          </template>
                          <v-list-item-group v-for="(history, j) in item.wallet.historyTransactions" :key="j">
                            <v-list-item>
                              <v-list-item-title>Сумма пополнения: {{history.sum}}</v-list-item-title>
                              <v-list-item-title>Дата пополнения: {{history.date}}</v-list-item-title>
                              <v-list-item-title>Дата изменения строки: {{history.lastUpdateRow}}</v-list-item-title>
                            </v-list-item>
                          </v-list-item-group>
                        </v-list-group>
                        <v-list-item else>
                          <v-list-item-title>Пополнений не найдено</v-list-item-title>
                        </v-list-item>
                      </v-list-group>
                      <!--Отображение стиральных машин связанных с юзером-->
                      <v-list-group sub-group no-action v-if="item.machine.length > 0">
                        <template v-slot:activator>
                          <v-list-item-content>
                            <v-list-item-title>{{item.machine.name}} {{item.machine.capacity}}</v-list-item-title>
                          </v-list-item-content>
                        </template>
                        <v-list-item>
                          <v-list-item-title>Описание: {{item.machine.description}}</v-list-item-title>
                        </v-list-item>
                        <v-list-item>
                          <v-list-item-title>Цена: {{item.machine.price}}</v-list-item-title>
                        </v-list-item>
                        <v-list-item>
                          <v-list-item-title>Дата измений строки: {{item.machine.lastUpdateRow}}</v-list-item-title>
                        </v-list-item>
                      </v-list-group>
                      <v-list-group sub-group no-action v-else>
                        <template v-slot:activator>
                          <v-list-item-content>
                            <v-list-item-title>Информация о стиральных машинах</v-list-item-title>
                          </v-list-item-content>
                        </template>
                        <v-list-item>
                          <v-list-item-title>Стиральная машина не используется</v-list-item-title>
                        </v-list-item>
                      </v-list-group>
                    </v-list-group>
                  </v-list>
                </v-tab-item>
              </v-tabs>
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
const ip = "192.168.0.113"
const port = '9000'
const axios = require('axios')

export default {
  name: "AdminCabinet",
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
      actionTable: [
          'Создать', 'Редактировать', 'Удалить'
      ],
      activeAction: null,
      fullInfo: false,
      info: {
        machines: null,
        persons: null,
        wallets: null,
        histories: null,
        stocks: null
      }
    }
  },
  computed: {
    getMappedItems() {
      return this.chosenTableInfo.map(i => i.id + ' - ' + i.login);
    }
  },
  methods: {
    handleTable(ev) {
      this.fullInfo = true;
      this.chosenTable = ev.path[0].offsetParent.offsetParent.attributes.getNamedItem('name').value || ev.path[0].offsetParent.attributes.getNamedItem('name').value
      this.doReq(this.chosenTable);
    },
    doReq(nameTable) {
      axios.get(`http://${ip}:${port}/api/${nameTable.toLowerCase()}`)
        .then(resp => (this.chosenTableInfo = resp.data))
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
          } else {
            this.tablesInfo.countsRows.push(0)
          }
          this.tablesInfo.datesEdits.push(resp.data[resp.data.length-1].lastUpdateRow)
        })
    axios.get(`http://${ip}:${port}/api/drafts`)
        .then(resp => {
          if (resp.data.length > 0) {
            this.tablesInfo.countsRows.push(resp.data.length)
          } else {
            this.tablesInfo.countsRows.push(0)
          }
          this.tablesInfo.datesEdits.push(resp.data[resp.data.length-1].lastUpdateRow)
        })
    axios.get(`http://${ip}:${port}/api/stocks`)
        .then(resp => {
          if (resp.data.length > 0) {
            this.tablesInfo.countsRows.push(resp.data.length)
            this.info.stocks = resp.data
          } else {
            this.tablesInfo.countsRows.push(0)
          }
          this.tablesInfo.datesEdits.push(resp.data[resp.data.length-1].lastUpdateRow)
        })
    axios.get(`http://${ip}:${port}/api/wallets`)
        .then(resp => {
          if (resp.data.length > 0) {
            this.tablesInfo.countsRows.push(resp.data.length)
            this.info.wallets = resp.data
          } else {
            this.tablesInfo.countsRows.push(0)
          }
          this.tablesInfo.datesEdits.push(resp.data[resp.data.length-1].lastUpdateRow)
        })
    axios.get(`http://${ip}:${port}/api/histories`)
        .then(resp => {
          if (resp.data.length > 0) {
            this.tablesInfo.countsRows.push(resp.data.length)
            this.info.histories = resp.data
          } else {
            this.tablesInfo.countsRows.push(0)
          }
          this.tablesInfo.datesEdits.push(resp.data[resp.data.length-1].lastUpdateRow)
        })
  }
}
</script>

<style scoped>

</style>