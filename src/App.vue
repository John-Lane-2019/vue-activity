<template>
 <div id="activityApp">
    <nav class="navbar is-white topNav">
      <div class="container">
        <div class="navbar-brand">
          <h1>{{ fullAppName }}</h1>
        </div>
      </div>
    </nav>
    <TheNavbar/>
    <section class="container">
      <div class="columns">
        <div class="column is-3">
        <ActivityCreate @activityCreated="addActivity" :categories="categories"/>
        </div>
        <div class="column is-9">
          <div class="box content" 
               :class="{fetching: isFetching, 'has-error': error }">
            <div v-if="error">
              {{ error }}
            </div>
            <div v-else>
              <div v-if="isFetching">
                Loading...
              </div>
              <ActivityItem 
              v-for="activity in activities"
              :activity="activity"
              :key="activity.id"
            />
            </div>
            <div v-if="!isFetching">
              <div class="activity-length">Currently {{ activityLength }} Activities</div>
            </div>
            <div class="activity-motivation">{{ activityMotivation }}</div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>  
<script>
import Vue from 'vue'
import ActivityItem from '@/components/ActivityItem'
import ActivityCreate from '@/components/ActivityCreate'
import { fetchActivities, fetchUser, fetchCategories } from '@/api'
import TheNavbar from '@/components/TheNavbar'
export default {
  name: 'app',
  components: {ActivityItem, ActivityCreate, TheNavbar},
  data () {
    return {
      creator: 'John Lane',
      appName: 'Activity Planner',
      watchedAppName: 'Activity Planner by John Lane',
      isFetching: false,
      error: null,
      user: {},
      activities: {},
      categories: {},
    }
  },
  computed:{
    fullAppName(){ 
      return `${this.appName} by ${this.creator}`
    },
    activityLength(){
      return Object.keys(this.activities).length
    },
    activityMotivation(){
      if (this.activityLength && this.activityLength< 5) {
        return "Nice to see some motivation!"
      }else if(this.activityLength >= 5){
        return "Wow, a real go-getter!"
      }else{
        return "No activities?? Too bad :("
      }
    }
  },
  created(){
    this.isFetching = true 
    fetchActivities()
      .then( activities =>{
        this.activities = activities
        this.isFetching = false 
      })
      .catch(err => {
        this.error = err 
        this.isFetching = false 
      })
    this.user = fetchUser()
    this.categories = fetchCategories()
  },
  methods: {
    addActivity(newActivity){
      Vue.set(this.activities, newActivity.id, newActivity)
      /*allows you to modify a reactive object with properties that are also reactive
      3 parameters: object to be modified, a key (so property is dynamic), the value
      we want to set it to.*/
    }
  }
}
</script>
<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
html,body {
  font-family: 'Open Sans', serif;
  background: #F2F6FA;
}
footer {
  background-color: #F2F6FA !important;
}
.fetching {
  border: 2px solid orange;
}
.has-error {
  border: 2px solid red;
}
.example-wrapper {
  margin-left: 30px;
}
.topNav {
  border-top: 5px solid #3498DB;
}
.topNav .container {
  border-bottom: 1px solid #E6EAEE;
}
.container .columns {
  margin: 3rem 0;
}
.navbar-menu .navbar-item {
  padding: 0 2rem;
}
aside.menu {
  padding-top: 3rem;
}
aside.menu .menu-list {
  line-height: 1.5;
}
aside.menu .menu-label {
  padding-left: 10px;
  font-weight: 700;
}
.button.is-primary.is-alt {
  background: #00c6ff;
  background: -webkit-linear-gradient(to bottom, #0072ff, #00c6ff);
  background: linear-gradient(to bottom, #0072ff, #00c6ff);
  font-weight: 700;
  font-size: 14px;
  height: 3rem;
  line-height: 2.8;
}
.media-left img {
  border-radius: 50%;
}
.media-content p {
  font-size: 14px;
  line-height: 2.3;
  font-weight: 700;
  color: #8F99A3;
}
article.post {
  margin: 1rem;
  padding-bottom: 1rem;
  border-bottom: 1px solid #E6EAEE;
}
article.post:last-child {
  padding-bottom: 0;
  border-bottom: none;
}
.menu-list li{
  padding: 5px;
}
.navbar-brand > h1 {
  font-size: 31px;
  padding: 20px;
}
.activity-length {
  display:inline-block
}
.activity-motivation {
  float: right;
}
</style>
