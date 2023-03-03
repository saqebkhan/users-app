<template>
  <div>
    <div class="loading-bar" v-if="loading">
      <div class="progress"></div>
    </div>
    <h1>Users</h1>
    <button
      @click="startEdit"
      style="float: right; margin: 12px; margin-top: 10px"
    >
      Add User
    </button>
    <table>
      <thead v-if="state.users.length">
        <tr>
          <th>Name</th>
          <th>Email</th>
          <th>Phone</th>
          <th>City</th>
          <th>Created By</th>
          <th>Created At</th>
          <th>Payment Status</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in state.users" :key="user._id">
          <td>{{ user.name }}</td>
          <td>{{ user.email }}</td>
          <td>{{ user.phone }}</td>
          <td>{{ user.city }}</td>
          <td>{{ user.createdBy }}</td>
          <td>{{ user.createdAt }}</td>
          <td style="align-items: center">
            <div :class="user.paymentStatus ? 'circle' : 'red'"></div>
            {{ user.paymentStatus ? "Paid" : "Due" }}
          </td>
          <td>
            <button @click="editUser(user)">Edit</button>
            <button @click="deleteUser(user)">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>
    <form
      :class="state.showForm ? 'show-form' : 'remove-form'"
      v-if="state.isEditMode"
      @submit.prevent="saveUser"
    >
      <input
        id="name"
        v-model="state.editedUser.name"
        placeholder="Name"
        required
      />
      <input
        id="email"
        v-model="state.editedUser.email"
        placeholder="Email"
        required
      />
      <input
        id="phone"
        v-model="state.editedUser.phone"
        placeholder="Phone No."
        required
      />
      <input
        id="city"
        v-model="state.editedUser.city"
        placeholder="City"
        required
      />
      <input
        id="createdBy"
        v-model="state.editedUser.createdBy"
        placeholder="Created By"
        required
      />
      <select
        id="paymentStatus"
        v-model="state.editedUser.paymentStatus"
        placeholder="Payment Status"
        required
      >
        <option value="true">Paid</option>
        <option value="false">Unpaid</option>
      </select>
      <button type="submit">Save</button>
      <button @click="cancelEdit" type="reset">Cancel</button>
    </form>
  </div>
</template>

<script>
import { reactive, onMounted, watch, ref } from "vue";
import axios from "axios";

export default {
  name: "UserList",
  setup() {
    const state = reactive({
      users: [],
      items: [],
      isEditMode: false,
      showForm: true,
      editedUser: {
        name: "",
        email: "",
        phone: "",
        city: "",
        createdBy: "",
        paymentStatus: true,
      },
    });
    const loading = ref(false)

    const getUsers = async () => {
      loading.value = true;
      try {
        const response = await axios.get(
          "https://users-api-alpha.vercel.app/users"
        );
        state.users = response.data;
      } catch (error) {
        console.log(error);
      }
      loading.value = false;
    };
    const editUser = (user) => {
      state.isEditMode = true;
      state.editedUser = Object.assign({}, user);
    };

    const saveUser = async () => {
      try {
        if (state.editedUser._id) {
          await axios.put(
            `https://users-api-alpha.vercel.app/users/${state.editedUser._id}`,
            state.editedUser
          );
        } else {
          await axios.post(
            "https://users-api-alpha.vercel.app/users",
            state.editedUser
          );
        }
        state.showForm = false;
        setTimeout(() => {
          state.isEditMode = false;
          state.showForm = true;
          state.editedUser = {
            name: "",
            email: "",
            phone: "",
            city: "",
            createdBy: "",
            paymentStatus: true,
          };
        }, 200);
        await getUsers();
        console.log("saving")
      } catch (error) {
        console.log(error);
      }
    };

    const deleteUser = async (user) => {
      try {
        await axios.delete(
          `https://users-api-alpha.vercel.app/users/${user._id}`
        );
        await getUsers();
      } catch (error) {
        console.log(error);
      }
    };

    const startEdit = () => {
      state.isEditMode = true;
      state.editedUser = {
        name: "",
        email: "",
        phone: "",
        city: "",
        createdBy: "",
        paymentStatus: true,
      };
    };

    const cancelEdit = () => {
      state.showForm = false;
      setTimeout(() => {
        state.isEditMode = false;
        state.showForm = true;
        state.editedUser = {
          name: "",
          email: "",
          phone: "",
          city: "",
          createdBy: "",
          paymentStatus: true,
        };
      }, 200);
    };
    watch(loading, (newValue, oldValue) => {
      console.log(`count changed from ${oldValue} to ${newValue}`)
    })
    onMounted(() => {
      getUsers();
      console.log("mounted")
    });

    return {
      state,
      getUsers,
      editUser,
      saveUser,
      deleteUser,
      startEdit,
      cancelEdit,
      loading,
    };
  },
};
</script>

<style>
body {
  font-family: Arial, sans-serif;
  background-color: #f5f5f5;
}

h1 {
  font-size: 2em;
  margin-bottom: 20px;
  text-align: center;
  color: #333;
  display: inline-block;
  margin-left: 800px;
}

@keyframes drop {
  0% {
    transform: translateY(-100%);
    opacity: 0;
  }
  100% {
    transform: translateY(0);
    opacity: 1;
  }
}
@keyframes back {
  100% {
    transform: translateY(-100%);
    opacity: 0;
  }
  0% {
    transform: translateY(0);
    opacity: 1;
  }
}
table {
  border-collapse: collapse;
  width: 100%;
  margin-bottom: 30px;
  background-color: #fff;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  animation-name: drop;
  animation-duration: 0.1s;
  animation-timing-function: ease-in-out;
}

th,
td {
  padding: 12px;
  text-align: left;
  border-bottom: 1px solid #ddd;
}

th {
  background-color: #f5f5f5;
  font-weight: bold;
}

tr:hover {
  background-color: #f5f5f5;
}

button {
  background-color: rgb(110, 121, 214);
  color: #fff;
  border-radius: 4px;
  border: 1px solid rgb(110, 121, 214);
  padding: 8px 16px;
  margin-right: 10px;
  font-size: 14px;
}

button:hover {
  background-color: rgb(92, 103, 199);
}

.show-form {
  width: 100%;
  margin-bottom: 30px;
  animation-name: drop;
  animation-duration: 0.2s;
  animation-timing-function: ease-in-out;
}

.remove-form {
  width: 100%;
  margin-bottom: 30px;
  animation-name: back;
  animation-duration: 0.21s;
  animation-timing-function: ease-in-out;
}
input,
select {
  width: 200px;
  padding: 8px;
  margin: 10px;
  border: 1px solid rgb(188, 188, 188);
  border-radius: 4px;
  box-sizing: border-box;
}

input:focus,
select:focus {
  outline: none;
  box-shadow: 0 0 5px #333333;
}

.loading-bar {
  width: 100%;
  /* height: 4px; */
  background-color: #ddd;
  position: relative;
}

.progress {
  position: absolute;
  top: 0;
  left: 0;
  height: 4px;
  background-color: #4caf50;
  animation: loading 2s ease-in-out infinite;
}

@keyframes loading {
  0% {
    width: 0;
  }
  50% {
    width: 50%;
  }
  100% {
    width: 100%;
  }
}
.circle {
  width: 20px;
  height: 20px;
  border-radius: 70%;
  background-color: rgb(138, 255, 208);
}
.red {
  width: 20px;
  height: 20px;
  border-radius: 70%;
  background-color: rgb(255, 107, 107);
}
</style>
