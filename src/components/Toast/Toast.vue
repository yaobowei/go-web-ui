
<template>
  <transition name="fade">
    <div v-if="visible" class="toast">
      {{ message }}
    </div>
  </transition>
</template>

<script>
export default {
  data() {
    return {
      visible: false,
      message: '',
      duration: 2000
    }
  },
  methods: {
    show(msg, duration = 2000) {
      this.message = msg
      this.duration = duration
      this.visible = true
      setTimeout(() => {
        this.visible = false
      }, this.duration)
    }
  }
}
</script>

<style scoped>
.toast {
  position: fixed;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  padding: 12px 24px;
  background: rgba(0,0,0,0.7);
  color: white;
  border-radius: 4px;
  z-index: 9999;
}
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.3s;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
}
</style>
