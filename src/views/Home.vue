<template>
  <div class="text-center">
    <users-table />
    <add-user-modal-window />
  </div>
</template>

<script>
  import usersTable from "../components/UsersTable.vue";
  import addUserModalWindow from "../components/AddUserModalWindow.vue";

  export default {
    components: {
      usersTable,
      addUserModalWindow
    },
  }
</script>
