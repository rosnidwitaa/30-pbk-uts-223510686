<template>
  <div>
    <header>
      <nav>
        <ul>
          <li><button @click="showTodos">Todos</button></li>
          <li><button @click="showPosts">Posts</button></li>
        </ul>
      </nav>
    </header>

    <div v-if="showTodosContent">
      <!-- Konten Todos -->
      <div class="task-list">
        <h2>Daftar Kegiatan</h2>
        <ul>
          <li v-for="(task, index) in tasks" :key="index">
            <input type="checkbox" v-model="task.done">
            <span :class="{ 'text-decoration-line-through': task.done }">{{ task.name }}</span>&nbsp;
            <button @click="deleteTask(index)">Hapus</button>
          </li>
        </ul>
        <form @submit.prevent="addTask">
          <input type="text" v-model="newTaskName" placeholder="Masukkan Daftar Kegiatan">
          <button type="submit">Tambah Kegiatan</button>
        </form>
        <button @click="filterTasks">Tampilkan Kegiatan Yang Belum Selesai</button>
      </div>
    </div>

    <div v-else-if="showPostsContent">
      <!-- Konten Postingan -->
      <div class="post-list">
        <h2>Postingan Pengguna</h2>
        <select v-model="selectedUser" @change="loadUserPosts">
          <option value="">Pilih Pengguna</option>
          <option v-for="user in users" :key="user.id" :value="user.id">{{ user.name }}</option>
        </select>
        <ul v-if="userPosts.length">
          <li v-for="post in userPosts" :key="post.id">
            <h3>{{ post.title }}</h3>
            <p>{{ post.body }}</p>
          </li>
        </ul>
        <p v-else>Jumlah postingan tidak ada atau pengguna belum dipilih.</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tasks: [],
      newTaskName: '',
      showTodosContent: true,
      showPostsContent: false,
      users: [],
      selectedUser: null,
      userPosts: []
    }
  },
  mounted() {
    // Fetch users data
    fetch('https://jsonplaceholder.typicode.com/users')
      .then(response => response.json())
      .then(users => {
        this.users = users;
      });
  },
  methods: {
    addTask() {
      this.tasks.push({ name: this.newTaskName, done: false });
      this.newTaskName = '';
    },
    deleteTask(index) {
      this.tasks.splice(index, 1);
    },
    filterTasks() {
      this.tasks = this.tasks.filter(task => !task.done);
    },
    showTodos() {
      this.showTodosContent = true;
      this.showPostsContent = false;
    },
    showPosts() {
      this.showTodosContent = false;
      this.showPostsContent = true;
    },
    loadUserPosts() {
      if (!this.selectedUser) return;

      fetch(`https://jsonplaceholder.typicode.com/posts?userId=${this.selectedUser}`)
        .then(response => response.json())
        .then(posts => {
          this.userPosts = posts;
        });
    }
  }
}
</script>

<style scoped>
header {
  background-color: #333;
  color: white;
  padding: 10px 0;
  text-align: center;
}

nav ul {
  list-style-type: none;
  margin: 0;
  padding: 0;
}

nav ul li {
  display: inline;
  margin-right: 10px;
}

.task-list {
  width: 80%;
  margin: 40px auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.task-list h2 {
  margin-top: 0;
}

.task-list input[type="text"] {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  width: 100%;
}

.task-list button {
  background-color: palevioletred;
  color: white;
  border: none;
  border-radius: 5px;
  padding: 10px 20px;
  cursor: pointer;
}

.task-list button:hover {
  background-color: #DF5360;
}

.post-list {
  width: 80%;
  margin: 40px auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.post-list h2 {
  margin-top: 0;
}

.post-list select {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.post-list ul {
  list-style-type: none;
  padding: 0;
}

.post-list li {
  margin-bottom: 20px;
}

</style>
