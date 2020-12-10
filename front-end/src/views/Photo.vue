<template>
  <div class="page">
    <div v-if="photo" class="single-photo">
      <div class="image-box">
        <img id="selected-photo"  :src="this.photo.path" />
      </div>
      <div class="photoInfo">
        <p class="photoTitle">{{photo.title}}</p>
        <p class="photoName">Submitted by: {{photo.user.firstName}} {{this.photo.user.lastName}}</p>
      </div>
      <p class="photoDate">{{formatDate(photo.created)}}</p>
      <p v-if="error">{{error}}</p>
    </div>
    <div v-if="user" class="comment-section">
      <div v-for="comment in comments" v-bind:key="comment._id" class="comments">
        {{comment.thecomment}}
      </div>
      <textarea v-model="comment" placeholder="Comment"></textarea>
      <div @click="upload" class="submit-button">Submit</div>
      <div @click="deletePhoto(photo)" class="submit-button">Delete Photo</div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import moment from 'moment';
export default {
  name: 'photo',
  components: {

  },
  data() {
    return {
      photo: null,
      comments: [],
      comment: '',
      error: '',
    }
  },
  created() {
    console.log("created");
    this.getPhoto();
    this.getComments();
  },
  computed: {
    user() {
      return this.$root.$data.user;
    }
  },
  methods: {
    async deletePhoto (userPhoto) {
      try {
        await axios.delete("/api/photos/" + userPhoto._id);
        console.log(userPhoto._id)
      } catch (error) {
        this.error = error.response.data.message;
      }
      this.getPhoto();
    },
    async getPhoto() {
      try {
        let response = await axios.get("/api/photos/" + this.$route.params.id);
        this.photo = response.data;
      } catch (error) {
        this.error = error.response.data.message;
      }
    },
    async upload() {
      try {
          await axios.post('/api/comments/' + this.$route.params.id, {
          comment: this.comment,
          user: this.photo.user,
          photo: this.photo,
      });
      } catch (error) {
        console.log(error);
      }
      this.getComments();
      this.comment = "";
    },
    async getComments() {
      console.log("working")
      try {
        let response = await axios.get("/api/comments/" + this.$route.params.id);
        this.comments = response.data;
      } catch (error) {
        this.error = error.response.data.message;
      }
      console.log('working2')
    },
    formatDate(date) {
      if (moment(date).diff(Date.now(), 'days') < 15)
        return moment(date).fromNow();
      else
        return moment(date).format('d MMMM YYYY');
    },
  }
}
</script>
<style scoped>
  .page {
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .single-photo {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 100%;
  }
  .image-box {
    display: flex;
    justify-content: center;
    align-items: flex-start;
    max-width: 90vw;
  }
  #selected-photo {
    width: 100%;
    display: block;
  }
  .comment-section {
    margin-bottom: 50px;
  }
  .submit-button {
    width: 150px;
    margin: 20px;
    padding: 5px 10px;
    background-color: lightgrey;
  }

</style>
