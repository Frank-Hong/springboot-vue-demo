<template>
  <div>
    <!--  使用引入的组件Header  -->
    <Header/>
    <div style="display: flex">
      <Aside></Aside>
      <router-view style="flex: 1"/>
    </div>
  </div>

</template>

<style>

</style>

<script>
//引入外部组件Head
import Header from "./components/Header";
import Aside from "./components/Aside";
//当前组件的名称为Layout，它包含一个组件Header
export default{
  name:"Layout",
  components:{
    Header,
    Aside
  }
}
</script>