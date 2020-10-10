<template>
  <v-app>
    <v-card style="margin: 8% 15% 0 15%">
      <v-tabs grow color="indigo">
        <v-tab>Информация</v-tab>
        <v-tab>Мои заказы</v-tab>
        <v-tab>Настройки</v-tab>
        <v-tab-item>
          <v-card-title>Личный кабинет {{info.userInfo.fName}} {{info.userInfo.sName}}</v-card-title>
          <v-divider></v-divider>
          <v-container>
            <v-row>
              <v-col>
                <v-img width="240" height="240" :src="info.userInfo.avatar"></v-img>
              </v-col>
              <v-col>
                <v-card-text>
                  <b>Имя:</b> {{info.userInfo.fName}} <br/>
                  <b>Фамилия:</b> {{info.userInfo.sName}} <br/>
                  <b>Логин:</b> {{info.userInfo.login}} <br/>
                  <b>E-mail:</b> {{info.userInfo.email}} <br/>
                  <b>Телефон:</b> {{info.userInfo.phone}} <br/>
                  <b>Баланс:</b> {{info.userInfo.balance}}
                </v-card-text>
              </v-col>
            </v-row>
          </v-container>
          <v-divider></v-divider>
          <v-card flat>
            <v-card-title>Текущие стирки:</v-card-title>
            <v-divider></v-divider>
            <v-list v-if="info.userInfo.machine !== null">
              <v-list-group v-for="(item, i) in info.userInfo.machine" :key="i">
                <template v-slot:activator>
                    <v-list-item-title>
                      <v-badge :color="item.status" title="Статус стиральной машини">
                        {{item.name}}
                      </v-badge>
                    </v-list-item-title>
                  <v-item-group>
                    <v-list-item-title>Готово на {{item.percentReady}}%</v-list-item-title>
                  </v-item-group>
                </template>
                <v-list-item>
                  <v-container>
                    <v-row>
                      <v-col>
                        <v-img width="140" height="140" :src="item.img"></v-img>
                      </v-col>
                      <v-col>
                        <v-list-item-title>
                          Цена: {{item.price}} UAH <br/>
                          Описание: {{item.description}} <br/>
                          Вместимость: {{item.capacity}}л.
                        </v-list-item-title>
                      </v-col>
                    </v-row>
                  </v-container>
                </v-list-item>
              </v-list-group>
            </v-list>
            <div v-else style="margin: 10%">
              <v-icon style="text-align: center;display: block">warning</v-icon>
              <v-card-title style="justify-content: center">Активных заказов нет</v-card-title>
            </div>
          </v-card>
        </v-tab-item>
        <v-tab-item>
          <v-list v-if="info.drafts !== null">
            <v-list-group v-for="(item, i) in info.drafts" :key="i">
              <template v-slot:activator>
                <v-list-item-title>
                  {{item.machine.name}}
                </v-list-item-title>
                <v-item-group>
                  <v-list-item-title>{{item.date}}</v-list-item-title>
                </v-item-group>
              </template>
              <v-container>
                <v-row>
                  <v-col>
                    <v-text-field
                        label="Способ оплаты"
                        readonly
                        :value="item.paymentType === 'credit'?'Кредитная карта':'Наличные'"
                    ></v-text-field>
                    <v-text-field
                        v-if="item.paymentType === 'credit'"
                        label="Номер кредитной карты"
                        readonly
                        :value="item.creditCard"
                    ></v-text-field>
                  </v-col>
                  <v-col>
                    <v-badge :color="item.machine.status" overlap title="Статус стиральной машини на данный момент">
                      <v-card rounded style="padding: 5%">
                        <v-row>
                          <v-col>
                            <v-text-field
                                label="Стиральная машина"
                                readonly
                                :value="item.machine.name"
                            ></v-text-field>
                            <v-text-field
                                label="Вместимость"
                                readonly
                                :value="item.machine.capacity"
                            ></v-text-field>
                          </v-col>
                          <v-col>
                            <v-text-field
                                label="Цена"
                                readonly
                                :value="item.machine.price+' UAH/л.'"
                            ></v-text-field>
                            <v-text-field
                                label="Скидка"
                                readonly
                                :value="item.machine.stock"
                            ></v-text-field>
                          </v-col>
                        </v-row>
                        <v-select :items="item.additional" rounded label="Используемые добавки"></v-select>
                      </v-card>
                    </v-badge>
                  </v-col>
                </v-row>
              </v-container>
            </v-list-group>
          </v-list>
          <div v-else style="margin: 10%">
            <v-icon style="text-align: center;display: block">warning</v-icon>
            <v-card-title style="justify-content: center">Заказов не найдено</v-card-title>
          </div>
        </v-tab-item>
        <v-tab-item>
          <v-card-title>Настройки аккаунта</v-card-title>
          <v-divider></v-divider>
          <v-container>
            <v-row style="margin: 0 2% 0 2%">
              <v-col>
                <v-img :src="info.userInfo.avatar"></v-img>
                <v-file-input
                    accept="image/png, image/jpeg, image/jpg"
                    label="Ваша аватарка"
                    prepend-icon="mdi-camera"
                ></v-file-input>
              </v-col>
              <v-col>
                <v-row>
                  <v-col>
                    <v-text-field
                        label="Имя"
                        :placeholder="info.userInfo.fName"
                        outlined
                    ></v-text-field>
                    <v-text-field
                        label="Фамилия"
                        :placeholder="info.userInfo.sName"
                        outlined
                    ></v-text-field>
                    <v-text-field
                        label="Телефон"
                        :rules="phoneRules"
                        :placeholder="info.userInfo.phone"
                        outlined
                    ></v-text-field>
                  </v-col>
                  <v-col>
                    <v-text-field
                        label="Логин"
                        :placeholder="info.userInfo.login"
                        outlined
                    ></v-text-field>
                    <v-text-field
                        label="Пароль"
                        type="password"
                        :placeholder="info.userInfo.pwd"
                        outlined
                    ></v-text-field>
                    <v-text-field
                        label="E-mail"
                        type="email"
                        :rules="emailRules"
                        :placeholder="info.userInfo.email"
                        outlined
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-col>
            </v-row>
          </v-container>
          <v-btn @click="editSettings" outlined color="indigo" width="100%">ИЗМЕНИТЬ</v-btn>
        </v-tab-item>
      </v-tabs>
    </v-card>
  </v-app>
</template>

<script>
const ip = "192.168.0.113"
const port = "9000"
const axios = require('axios')
export default {
  name: "Cabinet",
  data() {
    return {
      info: {
        userInfo: null,
        drafts: null,
      },
      phoneRules: [
        v => v.length === 10 || 'Введите коретный номер телефона'
      ],
      emailRules: [
        v => v.match("[a-zA-Z]+@[a-zA-Z]+[.][a-zA-Z]+") || "Введите верный e-mail"
      ]
    }
  },
  methods: {
    editSettings() {

    }
  },
  mounted() {
    axios.get(`http://${ip}:${port}/api/persons/35`)
      .then(resp => {
        this.info.userInfo = resp.data
        axios.get(`http://${ip}:${port}/api/drafts`)
            .then(resp1 => {
              this.info.drafts = resp1.data.filter(i => i.person.id === resp.data.id);
            })
      })
  }
}
</script>

<style scoped>

</style>