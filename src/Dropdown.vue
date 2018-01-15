<template lang="pug">
  li(v-on:mouseenter="enter" , v-on:mouseleave="leave" ref="list")
    slot(name="title")
    transition(name="trigger")
      div.dropdown(v-show="show" ref="dropdown")
        slot
</template>

<script>
export default {
  name: 'DropDown',
  data() {
    return {
      show: false,
      topOffset: 0,
    };
  },
  methods: {
    enter(event) {
      this.toggle(event);
      this.$nextTick(() => {
        this.$emit('enter', this.$refs.dropdown);
      });
    },
    leave(event) {
      this.$emit('leave', this.$refs.dropdown);
      this.toggle(event);
    },
    toggle(event) {
      if (event.currentTarget === event.target) {
        this.show = !this.show;
      }
    },
  },
  mounted() {
    this.topOffset = this.$refs.list.getBoundingClientRect().height;
    this.$refs.dropdown.style.top = `${this.topOffset}px`;
  },
};
</script>

<style scoped>
.dropdown {
  position: absolute;
  padding: 20px;
  color: #555;
  will-change: opacity;
}

li {
  position: relative;
  display: flex;
  justify-content: center;
}

.trigger-enter,
.trigger-leave-to {
  opacity: 0;
}

.trigger-enter-active {
  transition: all 0.5s;
}
.trigger-leave-active {
  transition: all 0s;
}
</style>
