<template>
  <!--  使用引入的组件Header  -->
  <Header :user="user"/>
  <div style="display: flex">
    <Aside></Aside>
    <router-view style="flex: 1" @userInfo="refreshUser"/>
  </div>
</template>

<script>
//引入外部组件Head
import Header from "@/components/Header";
import Aside from "@/components/Aside";
import request from "../../utils/request";
export default {
  name: "Layout",
  components:{
    Header,
    Aside
  },
  data() {
    return {
      user: {}
    }
  },
  created() {
    this.refreshUser()
  },
  methods: {
    refreshUser() {
      let userJson = sessionStorage.getItem("user");
      if (!userJson) {
        return
      }
      let userId = JSON.parse(userJson).id
      request.get("/user/" + userId).then(res => {
        this.user = res.data
      })
    }
  }
}
</script>

<style scoped>

</style>