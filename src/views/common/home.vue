<template>
<!--  <div class="mod-home">
  </div>-->
  <div class="site-wrapper site-page--login">
    <div class="site-content__wrapper">
      <div class="site-content">
        <div class="brand-info">
         <!-- <h5 style="font-size: 24px;font-weight: 200;text-transform: uppercase;color: black">垃圾分类类别：</h5>
          <p class="brand-info__intro">厨余垃圾，是指居民日常生活中产生的有机类生活垃圾，包括固体食物、蛋壳、瓜果皮核、茶渣等厨余垃圾和农贸市场、农产品批发市场废弃的果蔬垃圾等。</p>
          <p class="brand-info__intro">可回收物，是指适宜回收和资源化利用的废弃物，包括玻璃、纸类、塑料、金属、大件垃圾和废旧织物等。</p>
          <p class="brand-info__intro">有害垃圾，是指对人体健康或者自然环境造成直接或者潜在危害的生活垃圾，包括废电池、废荧光灯管、废温度计、废血压计、废药品，废胶片等。</p>
          <p class="brand-info__intro">其他垃圾，是指除前三项以外的生活垃圾，包括灰土陶瓷、废弃卫生巾、一次性纸尿布、餐巾纸等难以回收利用的废弃物。</p>-->
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
  }
</script>

<style scoped>
  .mod-home {
    position: fixed;
    width: 100%;
    height: 100%;
    line-height: 1.5;
    /*background-color: #14988b;*/
    background: url(~@/assets/img/login_bg.jpg);
    background-size: 80% 80%;
  }
</style>

<style lang="scss">
  .site-wrapper.site-page--login {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background-color: rgba(38, 50, 56, .6);
    overflow: hidden;

    .site-content__wrapper {
      position: absolute;
      top: 0;
      right: 0;
      bottom: 0;
      left: 0;
      padding: 0;
      margin: 0;
      overflow-x: hidden;
      overflow-y: auto;
      background-color: transparent;
      background-image: url(~@/assets/img/login_bg.jpg);
      background-size: 100% 100%;
      opacity: 0.5;
    }
    .site-content {
      min-height: 100%;
      padding: 30px 500px 30px 30px;
    }
    .brand-info {
      margin: 220px 100px 0 90px;
      color: #fff;
    }
    .brand-info__text {
      margin:  0 0 22px 0;
      font-size: 48px;
      font-weight: 400;
      text-transform : uppercase;
    }
    .brand-info__intro {
      margin: 10px 0;
      font-size: 16px;
      line-height: 1.58;
      opacity: .6;
      color: black;
    }
  }
</style>
