<template>
  <div class="hello" style="background: cadetblue">
    <Button @click="hahaClick">haha</Button>
    <h1>{{ msg }}</h1>
    <p>
      For a guide and recipes on how to configure / customize this project,<br>
      check out the
      <a href="https://cli.vuejs.org" target="_blank" rel="noopener">vue-cli documentation</a>.
    </p>
    <h3>Installed CLI Plugins</h3>
    <ul>
      <li><a href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-babel" target="_blank"
             rel="noopener">babel</a></li>
      <li><a href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-eslint" target="_blank"
             rel="noopener">eslint</a></li>
    </ul>
    <h3>Essential Links</h3>
    <ul>
      <li><a href="https://vuejs.org" target="_blank" rel="noopener">Core Docs</a></li>
      <li><a href="https://forum.vuejs.org" target="_blank" rel="noopener">Forum</a></li>
      <li><a href="https://chat.vuejs.org" target="_blank" rel="noopener">Community Chat</a></li>
      <li><a href="https://twitter.com/vuejs" target="_blank" rel="noopener">Twitter</a></li>
      <li><a href="https://news.vuejs.org" target="_blank" rel="noopener">News</a></li>
    </ul>
    <h3>Ecosystem</h3>
    <ul>
      <li><a href="https://router.vuejs.org" target="_blank" rel="noopener">vue-router</a></li>
      <li><a href="https://vuex.vuejs.org" target="_blank" rel="noopener">vuex</a></li>
      <li><a href="https://github.com/vuejs/vue-devtools#vue-devtools" target="_blank" rel="noopener">vue-devtools</a>
      </li>
      <li><a href="https://vue-loader.vuejs.org" target="_blank" rel="noopener">vue-loader</a></li>
      <li><a href="https://github.com/vuejs/awesome-vue" target="_blank" rel="noopener">awesome-vue</a></li>
    </ul>
    <div>
      <img :src="require('@/assets/test.png')">
    </div>
    <div
        class="parent-container">
      <image-editor ref="imageEditor" :image-path="require('@/assets/test.png')"/>
      <!--      <image-editor ref="imageEditor2" :image-path="require('@/assets/human.png')"/>-->
      <!--      <image-editor ref="imageEditor"-->
      <!--                :image-path="'https://images.pexels.com/photos/26555594/pexels-photo-26555594.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1'">-->
      <!--      </image-editor>-->

    </div>
    <div>
      <button @click="exportImage">导出</button>
      <button @click="resetImage">重置</button>
    </div>
  </div>
</template>
<script>
import ScreenShot from 'js-web-screen-shot'
import ImageEditor from "@/components/ImageEditor";

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  components: {
    ImageEditor
  },
  methods: {
    hahaClick() {
      const screenShotHandler = new ScreenShot({
        // 是否启用webrtc，值为boolean类型，值为false则使用html2canvas来截图
        enableWebRtc: false,
        // 层级级别，这个要比你页面上其他元素的z-index的值大，不然截不了
        level: 2001,
        completeCallback: this.callback, // 截图成功完成的回调
        closeCallback: this.closeFn // 截图取消的回调
      })
    },
    resetImage() {
      this.$refs.imageEditor.resetImage();
      // this.$refs.imageEditor2.resetImage();

    },
    exportImage() {
      this.$refs.imageEditor.exportImage();
      // this.$refs.imageEditor2.exportImage();
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.parent-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh; /* 可根据需要调整高度 */
}
h3 {
  margin: 40px 0 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
