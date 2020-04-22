<template>
  <div >
    <button @click="sMessage = !sMessage" type="button" class="btn btn-info">Write Messages</button>
    <form v-if="sMessage" @submit.prevent="addMessage">
      <div v-if="error" class="alert alert-dismissible alert-danger">
        <button type="button" class="close" data-dismiss="alert">&times;</button>
        <strong>Error!</strong>
        <p class="mb-0">{{error}}</p>
      </div>
      <div class="form-group">
        <label for="username">Username</label>
        <input v-model="message.username" type="text" class="form-control" id="username" required>
      </div>
      <div class="form-group">
        <label for="subject">Subject</label>
        <input v-model="message.subject" type="text" class="form-control" id="subject" required>
      </div>
      <div class="form-group">
        <label for="message">Message</label>
        <textarea v-model="message.message" class="form-control" id="message" rows="3"></textarea>
      </div>
      <div class="form-group">
        <label for="image">Image URL</label>
        <input v-model="message.imageURL" type="url" class="form-control" id="imageURL">
      </div>
      <button type="submit" class="btn btn-primary">Add Message</button>
    </form>
    <div class="list-unstyled" v-for="message in reverserdMessages" :key="message._id">
      <li class="media">
        <img :src="message.imageURL" class="mr-3" :alt="message.subject">
        <div class="media-body">
          <h4 class="mt-0 mb-1">{{message.username}}</h4>
          <h5 class="mt-0 mb-1">{{message.subject}}</h5>
          {{message.message}}
          <br />
          <small>{{message.created}}</small>
        </div>
      </li>
      <hr>
    </div>
  </div>
</template>

<script>
const API_URL = 'http://localhost:1234/messages';
export default {
  name: 'home',
  data: () => ({
    sMessage: false,
    error: '',
    messages: [],
    message: {
      username: 'Anonymous',
      subject: '',
      message: '',
      imageURL: '',
    },
  }),
  computed: {
    reverserdMessages() {
      return this.messages.slice().reverse();
    },
  },
  mounted() {
    fetch(API_URL).then((response) => response.json()).then((result) => {
      this.messages = result;
    });
  },
  methods: {
    addMessage() {
      console.log(this.messages);
      fetch(API_URL, {
        method: 'POST',
        body: JSON.stringify(this.message),
        headers: {
          'content-type': 'application/json',
        },
      }).then((response) => response.json()).then((result) => {
        if (result.details) {
          const error = result.details.map((detail) => detail.message).join(' ');
          this.error = error;
        } else {
          this.error = '';
          this.sMessage = false;
          this.message.push(result);
        }
      });
    },
  },
};
</script>

<style>
hr{
  border-top: 1px solid white;
}

img{
  max-width: 300px;
  height: auto;
}
button{
  margin-top: 3px;
  margin-bottom: 3px;
}
</style>
