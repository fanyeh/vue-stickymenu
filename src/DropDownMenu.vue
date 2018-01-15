<template lang="pug">
  div#drop
    nav.top(ref="navigation")
      .background(ref="background")
      ul.menu
        li
          slot(:name="logo")
        li.menu-item(v-for="(menuItem ,index) in menu")
          a(href="#") {{menuItem}}
          div.dropdown 
            slot(:name="index")
        li
          slot(:name="secondary")
            
              //- - for(var i = 0; i < 5 ;i++) {
              //-     div submenu1
              //- - }
              
        //- li.menu-item
        //-   a(href="#") menu2
        //-   div.dropdown submenu2

</template>

<script>
export default {
  props: {
    menu: {
      type: Array,
      required: true,
    },
  },
  mounted() {
    console.log(this.menu);
    this.$refs.navigation.addEventListener('mouseover', this.handleEnter);
    const menuItem = this.$el.querySelectorAll('.menu-item');
    menuItem.forEach(item => {
      item.addEventListener('mouseleave', this.handleOut);
    });
  },
  data() {
    return {
      enterNavigation: false,
    };
  },
  methods: {
    handleEnter(e) {
      let clickedNode = e.target;
      if (clickedNode.classList.contains('menu-item')) {
        // TODO: Close menu if menu-item has no child
        clickedNode.classList.add('trigger-enter');
        this.$refs.background.classList.add('open');
        setTimeout(() => {
          if (clickedNode.classList.contains('trigger-enter')) {
            clickedNode.classList.add('trigger-enter-active');
          }
        }, 150);

        const dropdownCoords = clickedNode.childNodes[1].getBoundingClientRect();
        const navCoords = this.$refs.navigation.getBoundingClientRect();
        const coords = {
          height: dropdownCoords.height,
          width: dropdownCoords.width,
          top: dropdownCoords.top - navCoords.top,
          left: dropdownCoords.left - navCoords.left,
        };
        this.setBackgroundProperty(coords);
      } else if (e.target === e.currentTarget || e.target.classList.contains('menu')) {
        if (this.enterNavigation) {
          this.enterNavigation = false;
          this.$refs.background.style.setProperty('transform', `rotateX(-90deg)`);
        }
      }
    },
    handleOut(e) {
      if (e.target.classList.contains('menu-item')) {
        e.target.classList.remove('trigger-enter', 'trigger-enter-active');
        this.$refs.background.classList.remove('open');
      }
    },
    setBackgroundProperty(coords) {
      if (this.enterNavigation) {
        this.$refs.background.style.setProperty(
          'transition',
          'left 0.2s,top 0.2s,width 0.2s, height 0.2s, opacity 0.2s, transform 0.2s',
        );
      } else {
        // Flip animation
        this.$refs.background.style.setProperty('transition', 'opacity 0.2s, transform 0.2s');
        this.$refs.background.style.setProperty('transform', `rotateX(0deg)`);
        this.enterNavigation = true;
      }
      this.$refs.background.style.setProperty('width', `${coords.width}px`);
      this.$refs.background.style.setProperty('height', `${coords.height}px`);
      this.$refs.background.style.setProperty('top', `${coords.top + 20}px`);
      this.$refs.background.style.setProperty('left', `${coords.left}px`);
    },
  },
};
</script>

<style scoped>
nav {
  position: relative;
  perspective: 2000px;
  padding: 10px 0 10px 0;
}

.dropdown {
  position: absolute;
  top: 32px;
  /* transform: translateY(100px); */
  display: none;
  transition: all 0.1s;
  opacity: 0;
  padding-left: 20px;
  padding-right: 20px;
  padding-bottom: 20px;
  padding-top: 40px;
}

.trigger-enter > .dropdown {
  display: block;
}

.trigger-enter-active > .dropdown {
  opacity: 1;
}

.background {
  width: 100px;
  height: 100px;
  position: absolute;
  background: #fff;
  border-radius: 4px;
  box-shadow: 0 50px 100px rgba(50, 50, 93, 0.1), 0 15px 35px rgba(50, 50, 93, 0.15),
    0 5px 15px rgba(0, 0, 0, 0.1);
  transition: width 0.5s, height 0.5s, opacity 0.1s, transform 0.1s;
  transform-origin: 50% 0;
  display: flex;
  justify-content: center;
  opacity: 0;
  transform: rotateX(-90deg);
  will-change: opacity, transform;
}

.background::before {
  content: '';
  position: absolute;
  width: 14px;
  height: 14px;
  display: block;
  background: white;
  /* top: -10px;
  left: 50%; */
  transform: translateY(-50%) rotate(45deg);
}

.background.open {
  opacity: 1;
}

nav ul {
  list-style: none;
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: center;
}

.menu > li {
  position: relative;
  display: flex;
  justify-content: center;
  margin: 0px;
  padding: 10px 20px;
}
</style>
