<template>
  <div>
    <h1>Users</h1>
    <div class="loading-bar" v-if="state.loading">
      <div class="progress"></div>
    </div>
    <table>
      <thead>
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
          <td>
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
    <form v-if="state.isEditMode" @submit.prevent="saveUser">
      <label for="name">Name:</label>
      <input id="name" v-model="state.editedUser.name" required />
      <label for="email">Email:</label>
      <input id="email" v-model="state.editedUser.email" required />
      <label for="phone">Phone:</label>
      <input id="phone" v-model="state.editedUser.phone" required />
      <label for="city">City:</label>
      <input id="city" v-model="state.editedUser.city" required />
      <label for="createdBy">Created By:</label>
      <input id="createdBy" v-model="state.editedUser.createdBy" required />
      <label for="paymentStatus">Payment Status:</label>
      <select id="paymentStatus" v-model="state.editedUser.paymentStatus" required>
        <option value="true">Paid</option>
        <option value="false">Unpaid</option>
      </select>
      <button>Save</button>
      <button @click="cancelEdit">Cancel</button>
    </form>
    <button @click="startEdit">Add User</button>
  </div>
</template>

<script>
import { reactive, onMounted } from "vue";
import axios from "axios";

export default {
  name: "UserList",
  setup() {
    const state = reactive({
      users: [],
      isEditMode: false,
      loading: false,
      editedUser: {
        name: "",
        email: "",
        phone: "",
        city: "",
        createdBy: "",
        paymentStatus: true,
      },
    });

    const getUsers = async () => {
      state.loading = true;
      try {
        const response = await axios.get("https://users-api-alpha.vercel.app/users");
        state.users = response.data;
      } catch (error) {
        console.log(error);
      }
      state.loading = false;
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
          await axios.post("https://users-api-alpha.vercel.app/users", state.editedUser);
        }
        state.isEditMode = false;
        state.editedUser = {
          name: "",
          email: "",
          phone: "",
          city: "",
          createdBy: "",
          paymentStatus: true,
        };
        await getUsers();
      } catch (error) {
        console.log(error);
      }
    };

    const deleteUser = async (user) => {
      try {
        await axios.delete(`https://users-api-alpha.vercel.app/users/${user._id}`);
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
      state.isEditMode = false;
      state.editedUser = {
        name: "",
        email: "",
        phone: "",
        city: "",
        createdBy: "",
        paymentStatus: true,
      };
    };

    onMounted(() => {
      getUsers();
    });

    return {
      state,
      getUsers,
      editUser,
      saveUser,
      deleteUser,
      startEdit,
      cancelEdit,
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
}

table {
  border-collapse: collapse;
  width: 100%;
  margin-bottom: 30px;
  background-color: #fff;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
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
  background-color: #333;
  color: #fff;
  border: none;
  padding: 8px 16px;
  margin-right: 10px;
  cursor: pointer;
  font-size: 14px;
}

button:hover {
  background-color: #555;
}

form {
  width: 100%;
  margin-bottom: 30px;
}

label {
  display: inline-block;
  width: 120px;
  margin-right: 10px;
}

input,
select {
  width: 200px;
  padding: 8px;
  margin-bottom: 10px;
  border: none;
  border-radius: 4px;
  box-sizing: border-box;
}

input:focus,
select:focus {
  outline: none;
  box-shadow: 0 0 4px #333;
}

button[type="submit"] {
  background-color: #333;
  color: #fff;
  border: none;
  padding: 8px 16px;
  margin-right: 10px;
  cursor: pointer;
  font-size: 14px;
  margin-top: 20px;
}

button[type="submit"]:hover {
  background-color: #555;
}

button[type="button"] {
  background-color: #ccc;
  color: #333;
  border: none;
  padding: 8px 16px;
  margin-right: 10px;
  cursor: pointer;
  font-size: 14px;
  margin-top: 20px;
}

button[type="button"]:hover {
  background-color: #ddd;
}
.loading-bar {
  width: 100%;
  height: 4px;
  background-color: #ddd;
  position: relative;
}

.progress {
  position: absolute;
  top: 0;
  left: 0;
  height: 4px;
  background-color: #4caf50; /* set your desired color */
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
  border: 1px solid rgb(107, 102, 102);
  background-color: rgb(0, 255, 149); 
}
.red {
  width: 20px;
  height: 20px;
  border-radius: 70%;
  border: 1px solid rgb(0, 0, 0);
  background-color: rgb(255, 79, 26);
}
</style>
