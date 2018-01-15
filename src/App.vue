<template lang="pug">
  #app
    nav(ref="navigation")
      ul( v-on:mouseover="mover" v-on:mouseleave="mover")
        .background(ref="background")
          span.arrow
        drop-down(v-on:enter="showEnter" v-on:leave="showLeave" v-for="n in 3" :key="n")
          a(href="#" slot="title") menu {{n}}
          div submenu1
          div submenu2
          div submenu3
</template>

<script>
import DropDown from './Dropdown';
export default {
  name: 'app',
  components: {
    DropDown,
  },
  data() {
    return {
      enterNavigation: false,
    };
  },
  methods: {
    showEnter(dropDownElement) {
      const dropdownCoords = dropDownElement.getBoundingClientRect();
      const navCoords = this.$refs.navigation.getBoundingClientRect();
      const coords = {
        height: dropdownCoords.height,
        width: dropdownCoords.width,
        top: dropdownCoords.top - navCoords.top,
        left: dropdownCoords.left - navCoords.left,
      };
      this.setBackgroundProperty(coords);
    },
    setBackgroundProperty(coords) {
      const styles = {};
      if (this.enterNavigation) {
        styles.transition =
          'left 0.2s,top 0.2s,width 0.2s, height 0.2s, opacity 0.2s, transform 0.2s';
      } else {
        // Flip animation
        styles.transition = 'opacity 0.2s, transform 0.2s';
        styles.transform = `rotateX(0deg)`;
        this.enterNavigation = true;
      }
      styles.width = `${coords.width}px`;
      styles.height = `${coords.height}px`;
      styles.top = `${coords.top}px`;
      styles.left = `${coords.left}px`;
      Object.assign(this.$refs.background.style, styles);
      this.$refs.background.classList.add('open');
    },
    showLeave() {
      this.$refs.background.classList.remove('open');
    },
    mover(e) {
      if (e.currentTarget === e.target) {
        this.$refs.background.style.setProperty('transform', `rotateX(-90deg)`);
        this.enterNavigation = false;
      }
    },
  },
};
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}

h1,
h2 {
  font-weight: normal;
}

a {
  color: #555;
  padding: 15px 20px;
}

nav {
  position: relative;
  perspective: 2000px;
}

nav ul {
  list-style: none;
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: center;
}

.background {
  width: 100px;
  height: 100px;
  position: absolute;
  border-radius: 4px;
  box-shadow: 0 50px 100px rgba(50, 50, 93, 0.1), 0 15px 35px rgba(50, 50, 93, 0.15),
    0 5px 15px rgba(0, 0, 0, 0.1);
  transition: width 0.5s, height 0.5s, opacity 0.1s, transform 0.1s;
  transform-origin: 50% 0;
  display: flex;
  justify-content: center;
  opacity: 0;
  transform: rotateX(-90deg);
  background-color: #ccc;
}

.background.open {
  opacity: 1;
}
.arrow {
  content: '';
  position: absolute;
  width: 14px;
  height: 14px;
  display: block;
  background: #ccc;
  transform: translateY(-50%) rotate(45deg);
}
</style>
