<template>
  <!--<div class="home">-->
    <!--<img alt="Vue logo" src="../assets/logo.png">-->
    <!--<HelloWorld msg="Welcome to Your Vue.js App"/>-->
  <!--</div>-->
  <div>
    <h1>防火墙策略申请</h1>
    <FirewallReq></FirewallReq>
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'
import FirewallReq from "@/components/FirewallReq";

export default {
  name: 'home',
  components: {
    FirewallReq,
    HelloWorld
  }
}
</script>
