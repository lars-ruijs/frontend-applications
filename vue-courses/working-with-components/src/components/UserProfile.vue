<template>
  <div class="user-profile">
    <div class="user-profile_user-panel">
        <h1 class="user-profile_user-name">@{{ user.username }}</h1>
        <div class="user-profile_admin-badge" v-if="user.isAdmin">
          Admin
        </div>
        <div class="user-profile_follower-count">
          <strong>Followers: </strong> {{ followers }}
        </div>
        <form class="user-profile_create-twoot" @submit.prevent="createNewTwoot">
          <label for="newTwoot"><strong>New Twoot</strong></label>
          <textarea name="newTwoot" id="NewTwoot" cols="30" rows="10" v-model="newTwootContent"></textarea>
          <div class="user-profile_create-twoot-type">
            <label for="newTwootType"><strong>Type: </strong></label>
            <select name="newTwootType" id="newTwootType" v-model="selectedTwootType">
              <option v-for="(option, index) in twootTypes" :key="index" :value="option.value">{{ option.name }}</option>
            </select>
          </div>
          <button>Twoot!</button>
        </form>
    </div>
    <div class="user-profile_twoots-wrapper">
      <TwootItem v-for="twoot in user.twoots" :key="twoot.id" :username="user.username" :twoot="twoot" @favourite="toggleFavourite"/>
    </div>
  </div>
</template>

<script>
import TwootItem from "./TwootItem";

export default {
  name: 'UserProfile',
  components: { TwootItem },
  // props: {
  //   msg: String
  // }
  data() {
    return {
      newTwootContent: '',
      selectedTwootType: 'instant',
      twootTypes: [{value: 'draft', name: 'Draft'}, {value: 'instant', name: 'Instant Twoot'}],
      followers: 0,
      user: {
        id: 1,
        username: 'lars',
        firstName: 'Lars',
        lastName: 'Ruijs',
        email: 'yoyoyoghurt@gmail.com',
        isAdmin: false,
        twoots: [{id: 1, content: "Twotter is cool"}, {id: 2, content: "Don't forget to like!"}]
      }
    }
  },
  watch: {
    followers(newFollowerCount, oldFollowerCount) {
      if (oldFollowerCount < newFollowerCount) {
        console.log(`${this.user.username} gained a follower!`);
      }
    }
  },
  computed: {
    fullName() {
      return `${this.user.firstName} ${this.user.lastName}`;
    }
  },
  methods: {
    followUser() {
      this.followers++;
    },
    toggleFavourite(id) {
      console.log(`Favourite ${id}`);
    },
    createNewTwoot() {
      if (this.newTwootContent && this.selectedTwootType !== "draft") {
        this.user.twoots.unshift ( {id: this.user.twoots.length + 1, content: this.newTwootContent
        });
        this.newTwootContent = '';
      }
    }
  },
  mounted() {
    this.followUser();
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .user-profile {
    display: gird;
    grid-template-columns: 1fr 3fr;
    width: 100%;
    padding: 50px 5%;
  }

  .user-profile_user-panel {
    display: flex;
    flex-direction: column;
    margin-right: 50px;
    padding: 20px;
    background-color: #fff;
    border-radius: 5px;
    border: 1px solid #DFE3E8;
  }

  .user-profile_admin-badge {
    color: blue;
  }
  .user-profile_create-twoot {
    display: flex;
    flex-direction: column;
  }
</style>
