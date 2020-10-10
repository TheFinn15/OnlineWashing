<template>
  <v-card max-width="220">
    <v-badge :color="machine.status">
      <v-img :src="machine.img" width="220" height="220"></v-img>
    </v-badge>
    <v-list>
      <v-list-group>
        <template v-slot:activator>
          <v-card-title>{{machine.name}}</v-card-title>
        </template>
        <v-list-item>
          <v-list-item-content>
            <v-card-text>
              <b>Вместимость</b>: {{machine.capacity}} л. <br />
              <b>Описание</b>: {{machine.description}} <br />
              <b>Цена за 1 кг</b>: {{machine.price}} грн.<br />
            </v-card-text>
          </v-list-item-content>
        </v-list-item>
      </v-list-group>
    </v-list>
    <v-btn width="100%" color="primary">Заказать</v-btn>
  </v-card>
</template>

<script>
export default {
  name: "Machine",
  props: {
    machine: {
      type: Object,
      required: true
    }
  }
}
</script>

<style scoped>

</style>