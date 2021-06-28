<template>
  <Viewer 
      width="90vw"
      height="90vh"
      @onControlVisible="onControlVisible" 
      :pages.sync="pages" 
      :startPage="startPage" />
</template>

<script>
  import Viewer from '../components/Viewer'

  export default {
    name: 'Home',
    components: {
      Viewer,
    },

    data() {
      return {
        controlIsVisible: false,
        pages: [
          require('../assets/1.jpg'),
          require('../assets/2.jpg'),
          require('../assets/3.jpg'),
        ],
        startPage: 0
      };
    },
    created() {
        setTimeout(() => {
            this.$root.$emit("onBackgroundChange", { background: "#000000" });
        }, 1);
    },
    destroyed() {
        this.$root.$emit("onBackgroundChange", { background: "#FFFFFF" });
    },
    methods: {
      onControlVisible(controlIsVisible) {
          this.controlIsVisible = controlIsVisible;
      },
    }
  }
</script>
