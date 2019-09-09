<template>
  <div>
    <section class="page11">
      <img src="../../static/images/boringbanner.png" alt="" width="100%">
    </section>
    <section class="page12">
      <img src="../../static/images/boring1.jpg" alt="" width="100%">
    </section>
    <section class="page12">
      <img src="../../static/images/boring2.jpg" alt="" width="100%">
    </section>
    <section class="page12">
      <img src="../../static/images/boring3.jpg" alt="" width="100%">
    </section>
    <section class="page12">
      <img src="../../static/images/boring4.jpg" alt="" width="100%">
    </section>
    <section class="page12">
      <img src="../../static/images/boring5.jpg" alt="" width="100%">
    </section>
    <section class="page12">
      <img src="../../static/images/boring6.jpg" alt="" width="100%">
    </section>
    <section class="page12">
      <img src="../../static/images/boring7.jpg" alt="" width="100%">
    </section>
    <section class="page12">
      <img src="../../static/images/boring8.jpg" alt="" width="100%">
    </section>
    <section class="page12">
      <img src="../../static/images/boring9.jpg" alt="" width="100%">
    </section>
    <section class="footer">
      <div class="box">
        <div class="left">
          <img src="../../static/images/price.jpg" alt="" width="100%">
        </div>
        <div class="right" id="btn" @click="intentRegister">
          立即报名
        </div>
      </div>
    </section>
  </div>
</template>

<script>
  export default {
    name: "RegisterPhone",
    methods: {
      intentRegister() {
        this.$router.push('registerPhone')
      }
    }
  }
</script>
<style scoped>
  @import "../../static/css/main.css";
</style>


