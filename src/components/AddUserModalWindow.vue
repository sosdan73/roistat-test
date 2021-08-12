<template>
  <v-dialog v-model="modalIsOpened" width="500">
    <template v-slot:activator="{ on, attrs }">
      <v-btn color="primary" dark v-bind="attrs" v-on="on" class="mt-8">
        Добавить
      </v-btn>
    </template>

    <v-card>
      <v-card-title class="text-h5 grey lighten-2">
        Добавление пользователя
      </v-card-title>
      <v-card-text>
        <div class="d-flex align-center gap-5">
          <label for="name" class="mr-4">
            Имя
          </label>
          <v-text-field id="name" v-model="userName"></v-text-field>
        </div>
        <div class="d-flex align-center gap-5">
          <label for="phone" class="mr-4">
            Телефон
          </label>
          <v-text-field id="phone" v-model="userPhone"></v-text-field>
        </div>
        <div class="d-flex align-center gap-5">
          <label for="superior" class="mr-4">
            Начальник
          </label>
          <v-select
            v-model="userSuperior"
            id="superior"
            :items="usersNames"
          ></v-select>
        </div>
      </v-card-text>

      <v-divider></v-divider>

      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="error" text @click="closeModalWindow">
          Закрыть
        </v-btn>
        <v-btn
          color="primary"
          text
          :disabled="!modalDataIsDefined"
          @click="saveUser"
        >
          Сохранить
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
import { mapMutations, mapState } from "vuex";
export default {
  data() {
    return {
      modalIsOpened: false,
      userName: "",
      userPhone: "",
      userSuperior: undefined
    };
  },
  methods: {
    ...mapMutations(["addUser"]),
    saveUser() {
      let superiorId = null;
      let subordinationLevel = 0;
      if (this.userSuperior) {
        const superiorIndex = this.users
          .map(user => user.name)
          .indexOf(this.userSuperior);
        const superior = this.users[superiorIndex];
        superiorId = superior.id;
        subordinationLevel = superior.subordinationLevel + 1;
      }

      let userId = 0;
      if (this.users.length > 0) {
        userId = Math.max(...this.users.map(user => user.id)) + 1;
      }

      this.addUser({
        id: userId,
        name: this.userName,
        phone: this.userPhone,
        superiorId: superiorId,
        subordinationLevel: subordinationLevel
      });
      this.updateUsersInLocalStorage();
      this.resetModalData();
    },
    closeModalWindow() {
      this.resetModalData();
    },
    updateUsersInLocalStorage() {
      localStorage.vueTestUsers = JSON.stringify(this.users);
    },
    resetModalData() {
      this.modalIsOpened = false;
      this.userName = "";
      this.userPhone = "";
      this.userSuperior = undefined;
    }
  },
  computed: {
    ...mapState({
      users: state => state.users
    }),
    modalDataIsDefined() {
      return Boolean(this.userName && this.userPhone);
    },
    usersNames() {
      return this.users.map(user => user.name);
    }
  }
};
</script>

<style lang="scss" scoped>
label {
  width: 100px;
}
</style>
