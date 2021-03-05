<template>
  <div>
  <button class="btn btn-info" @click="showMessageForm = !showMessageForm">
  Toggle Message Form</button>
  <form v-if="showMessageForm" @submit.prevent="addMessage">
  <div v-if="error" class="alert alert-dismissible alert-warning">
  <button type="button" class="close" data-dismiss="alert">&times;</button>
  <h4 class="alert-heading">Error!</h4>
  <p class="mb-0">{{ error }}</p>
</div>
  <div class="mb-3">
    <label for="username" class="form-label">Username</label>
    <input type="text" class="form-control"
     v-model="message.username" id="username" placeholder="Anonymous" required>
  </div>

  <div class="mb-3">
    <label for="subject" class="form-label">Subject</label>
    <input type="text" v-model="message.subject"
     class="form-control" id="subject" placeholder="Enter A subject" required>
  </div>

  <div class="mb-3">
    <label for="message" class="form-label">Message</label>
    <textarea type="text" v-model="message.message"
     class="form-control" rows="4" cols="4" id="message"></textarea>
  </div>

  <div class="mb-3">
    <label for="messageUrl" class="form-label">Image Url</label>
    <input type="url" class="form-control"
     v-model="message.imageUrl" id="imageUrl" required placeholder="Put an Image here">
  </div>

  <button type="submit" class="btn btn-primary">Submit</button>
</form>
<ul class="list-group">
  <li class="list-group-item d-flex justify-content-between
   align-items-center" v-for="message in reversedMessages" :key="message._id">
    <img v-if="message.imageUrl" :src="message.imageUrl"
     width="90" height="90" class="mr-3" :alt="message.subject">
    <h4>{{ message.username }}</h4>
    <h5>{{ message.subject }}</h5>
    <span class="badge bg-primary rounded-pill">{{ message.message }}</span>
    <br>
    <small>{{ message.created }}</small>
  </li>
</ul>
</div>
</template>

<script>

const API_URL = 'http://localhost:8000/messages';

export default {
  name: 'Home',
  data: () => ({
    showMessageForm: false,
    messages: [],
    error: '',
    message: {
      username: 'Anonymous',
      subject: '',
      message: '',
      imageUrl: '',
    },
  }),
  /* eslint-disable */
  computed:{
    reversedMessages(){
    return this.messages.slice().reverse()
    }
  },
  methods: {
    addMessage() {
      fetch(API_URL, {
        method: 'POST',
        body: JSON.stringify(this.message),
        headers: {
          'content-type': 'application/json',
        },
      }).then((response) => response.json()).then((result) => {
        if(result.details){
          // There was an error
          const error = result.details.map(detail => detail.message).join('.')
          this.error = error;

        }else{
          this.error = ''
          this.showMessageForm = false;
          this.messages.push(this.message)
        }
        
      });
      this.message = ''
    },
  },
  mounted() {
    fetch(API_URL).then((response) => response.json()).then((result) => {
      this.messages = result;
    });
  },
};
</script>
