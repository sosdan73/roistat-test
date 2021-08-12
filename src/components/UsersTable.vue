<template>
  <div>
    <table v-if="users && users.length > 0" class="text-left">
      <tr class="table-row">
        <th class="table-cell_name">
          <div class="table-cell_content">
            <h3
              class="d-flex"
              @click="setFilter('name')"
              @mouseenter="nameHeaderIsHovered = true"
              @mouseleave="nameHeaderIsHovered = false"
            >
              Имя
              <v-spacer></v-spacer>
              <v-icon
                class="icon"
                :color="filter.substr(0, 4) == 'name' ? 'black' : 'grey'"
                :class="{
                  'd-block':
                    nameHeaderIsHovered || filter.substr(0, 4) == 'name',
                  'd-none': !nameHeaderIsHovered,
                  'icon-upside-down': filter.indexOf('name-reverse') != -1
                }"
              >
                mdi-filter-variant
              </v-icon>
            </h3>
          </div>
        </th>
        <th class="table-cell_phone">
          <div class="table-cell_content">
            <h3
              class="d-flex"
              @click="setFilter('phone')"
              @mouseenter="phoneHeaderIsHovered = true"
              @mouseleave="phoneHeaderIsHovered = false"
            >
              Телефон
              <v-spacer></v-spacer>
              <v-icon
                class="icon"
                :color="filter.substr(0, 5) == 'phone' ? 'black' : 'grey'"
                :class="{
                  'd-block':
                    phoneHeaderIsHovered || filter.substr(0, 5) == 'phone',
                  'd-none': !phoneHeaderIsHovered,
                  'icon-upside-down': filter.indexOf('phone-reverse') != -1
                }"
              >
                mdi-filter-variant
              </v-icon>
            </h3>
          </div>
        </th>
      </tr>
      <tr class="table-row" :key="user.id" v-for="user in users">
        <td
          class="table-cell_name"
          :class="`pl-${user.subordinationLevel * 3}`"
        >
          <div class="table-cell_content">
            {{ user.name }}
          </div>
        </td>
        <td class="table-cell_phone">
          <div class="table-cell_content">
            {{ user.phone }}
          </div>
        </td>
      </tr>
    </table>

    <table v-else class="text-left">
      <tr class="table-row">
        <th class="table-cell_name">
          <div class="table-cell_content">
            <h3>Имя</h3>
          </div>
        </th>
        <th class="table-cell_phone">
          <div class="table-cell_content">
            <h3>Телефон</h3>
          </div>
        </th>
      </tr>
      <tr class="table-row">
        <td class="table-cell_name" colspan="2">
          <div class="table-cell_content">
            Пользователей не найдено
          </div>
        </td>
      </tr>
    </table>
  </div>
</template>

<script>
import { mapMutations, mapState } from "vuex";

export default {
  data() {
    return {
      filter: "none",
      filterEvolveTable: {
        name: {
          none: "name",
          name: "name-reverse",
          "name-reverse": "none",
          phone: "name",
          "phone-reverse": "name"
        },
        phone: {
          none: "phone",
          name: "phone",
          "name-reverse": "phone",
          phone: "phone-reverse",
          "phone-reverse": "none"
        }
      },
      nameHeaderIsHovered: false,
      phoneHeaderIsHovered: false
    };
  },
  created() {
    if (localStorage.vueTestUsers) {
      this.initializeUsers(JSON.parse(localStorage.vueTestUsers));
    }
  },
  methods: {
    ...mapMutations(["initializeUsers"]),
    setFilter(filterChosenValue) {
      this.filter = this.filterEvolveTable[filterChosenValue][this.filter];
    }
  },
  computed: {
    ...mapState({
      unsortedUsers: state => state.users
    }),
    users() {
      switch (this.filter) {
        case "none":
          return this.usersSortedBySuperior;
        case "name":
          return this.usersSortedByName;
        case "name-reverse":
          return this.usersSortedByNameReversed;
        case "phone":
          return this.usersSortedByPhone;
        case "phone-reverse":
          return this.usersSortedByPhoneReversed;
        default:
          return this.usersSortedBySuperior;
      }
    },
    usersSortedBySubordinationLevel() {
      const result = Array.from(this.unsortedUsers);
      if (result.length > 1) {
        result.sort(
          (user1, user2) => user1.subordinationLevel - user2.subordinationLevel
        );
      }
      return result;
    },
    usersSortedBySuperior() {
      const users = this.usersSortedBySubordinationLevel;
      if (users.length > 1) {
        const lastSortedUser = users[users.length - 1];
        if (lastSortedUser.subordinationLevel == 0) {
          return this.unsortedUsers;
        } else {
          const subordinatesUserIndex = users
            .map(user => user.subordinationLevel)
            .indexOf(1);
          const result = users.slice(0, subordinatesUserIndex);
          for (let i = subordinatesUserIndex; i < users.length; i++) {
            let currentUser = users[i];
            let superiorIndex = result
              .map(user => user.id)
              .indexOf(currentUser.superiorId);
            if (superiorIndex != -1) {
              result.splice(superiorIndex + 1, 0, currentUser);
            }
          }
          return result;
        }
      } else {
        return users;
      }
    },
    usersSortedByName() {
      const result = Array.from(this.unsortedUsers);
      if (result.length > 1) {
        result.sort((user1, user2) => {
          if (user1.name > user2.name) return 1;
          if (user1.name == user2.name) return 0;
          if (user1.name < user2.name) return -1;
        });
      }
      return result;
    },
    usersSortedByNameReversed() {
      const result = Array.from(this.usersSortedByName);
      return result.reverse();
    },
    usersSortedByPhone() {
      const result = Array.from(this.unsortedUsers);
      if (result.length > 1) {
        result.sort((user1, user2) => {
          if (user1.phone > user2.phone) return 1;
          if (user1.phone == user2.phone) return 0;
          if (user1.phone < user2.phone) return -1;
        });
      }
      return result;
    },
    usersSortedByPhoneReversed() {
      const result = Array.from(this.usersSortedByPhone);
      return result.reverse();
    }
  }
};
</script>

<style lang="scss" scoped>
table {
  border-collapse: collapse;
  min-width: 400px;
  max-width: 1000px;
  width: 80%;
  margin: auto;
}
.table-border {
  border: 1px solid black;
}
.table-cell_name {
  width: 40%;
}
.table-cell_phone {
  width: 60%;
}
.table-cell_content {
  border: 1px solid black;
  margin: -1px 0 0 -1px;
  padding: 5px 10px;
}
.icon-upside-down {
  transform: rotate(180deg);
}
</style>
