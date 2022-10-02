<template>
  <div class="tinder--cards">
    <div
      v-for="(item, index) in arrayData"
      :key="item"
      class="tinder--card"
      :style="{
        'z-index': arrayData.length - index,
        transform:
          'scale(' + (20 - index) / 20 + ') translateY(' + 30 * index + 'px)',
        opacity: (10 - index) / 10,
        'pointer-events': index < 1 ? 'auto' : 'none',
      }"
      v-hammer:pan="onPan"
      v-hammer:panstart="onPanStart"
      v-hammer:panend="onPanEnd"
    >
      Card {{ item }}
    </div>
  </div>
</template>

<script>
export default {
  name: "TinderSwipe",
  data() {
    return {
      arrayData: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
      xDown: null,
      yDown: null,
    };
  },
  methods: {
    initCards() {
      const newCards = document.querySelectorAll(".tinder--card:not(.removed)");

      newCards.forEach((card, index) => {
        card.style.zIndex = this.arrayData.length - index;
        card.style.transform =
          "scale(" + (20 - index) / 20 + ") translateY(" + 30 * index + "px)";
        card.style.opacity = (10 - index) / 10;
        card.style.pointerEvents = index < 1 ? "auto" : "none";
      });
    },
    onPan(event) {
      const target = event.target;
      const el = target.closest(".tinder--card");
      el.classList.add("moving");

      if (event.deltaX === 0) return;
      if (event.center.x === 0 && event.center.y === 0) return;

      const xMulti = event.deltaX * 0.03;
      const yMulti = event.deltaY / 80;
      const rotate = xMulti * yMulti;

      el.style.transform =
        "translate(" +
        event.deltaX +
        "px, " +
        event.deltaY +
        "px) rotate(" +
        rotate +
        "deg)";
    },
    onPanStart(event) {
      this.xDown = event.center.x;
      this.yDown = event.center.y;
    },
    onPanEnd(event) {
      const target = event.target;
      const el = target.closest(".tinder--card");

      el.classList.remove("moving");
      const moveOutWidth = document.body.clientWidth;
      const moveOutHeight = document.body.clientHeight;
      const keep =
        Math.abs(event.deltaX) > 80 ||
        Math.abs(event.deltaY) > 80 ||
        Math.abs(event.velocity) > 0.5;
      const moveX = Math.abs(event.deltaX) > 80;

      el.classList.toggle("removed", keep);

      if (!keep) {
        el.style.transform = "";
      } else {
        if (moveX) {
          const endX = Math.max(
            Math.abs(event.velocityX) * moveOutWidth,
            moveOutWidth
          );
          const toX = event.deltaX > 0 ? endX : -endX;
          const endY = Math.abs(event.velocityY) * moveOutWidth;
          const toY = event.deltaY > 0 ? endY : -endY;
          const xMulti = event.deltaX * 0.03;
          const yMulti = event.deltaY / 80;
          const rotate = xMulti * yMulti;
          el.style.transform =
            "translate(" +
            toX +
            "px, " +
            (toY + event.deltaY) +
            "px) rotate(" +
            rotate +
            "deg)";
        } else {
          const endY = Math.max(
            Math.abs(event.velocityY) * moveOutHeight,
            moveOutHeight
          );
          const toY = event.deltaY > 0 ? endY : -endY;
          const endX = Math.abs(event.velocityX) * moveOutHeight;
          const toX = event.deltaX > 0 ? endX : -endX;
          const yMulti = event.deltaY * 0.03;
          const xMulti = event.deltaX / 80;
          const rotate = xMulti * yMulti;

          el.style.transform =
            "translate(" +
            toX +
            "px, " +
            (toY + event.deltaX) +
            "px) rotate(" +
            rotate +
            "deg)";
        }
        this.initCards();

        if (!this.xDown || !this.yDown) return;

        const xUp = event.center.x;
        const yUp = event.center.y;

        let xDiff = this.xDown - xUp;
        let yDiff = this.yDown - yUp;
        // немного поясню здесь. Тут берутся модули движения по оси абсцисс и ординат (почему модули? потому что если движение сделано влево или вниз, то его показатель будет отрицательным) и сравнивается, чего было больше: движения по абсциссам или ординатам. Нужно это для того, чтобы, если пользователь провел вправо, но немного наискосок вниз, сработал именно коллбэк для движения вправо, а ни как-то иначе.
        if (Math.abs(xDiff) > Math.abs(yDiff)) {
          /*most significant*/
          if (xDiff > 0) {
            console.log("swipe left");
            /* left swipe */
          } else {
            console.log("swipe right");
            /* right swipe */
          }
        } else {
          if (yDiff > 0) {
            console.log("swipe up");
            /* up swipe */
          } else {
            console.log("swipe down");
            /* down swipe */
          }
        }
        /* reset values */
        this.xDown = null;
        this.yDown = null;
      }
    },
  },
};
</script>

<style scoped lang="scss">
.tinder--cards {
  height: 100%;
  flex-grow: 1;
  padding-top: 40px;
  text-align: center;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1;
}

.tinder--card {
  display: inline-block;
  width: 200px;
  height: 300px;
  border: 1px solid #aaa;
  padding: 5px 5px 40px;
  background: #eee;
  border-radius: 8px;
  overflow: hidden;
  position: absolute;
  will-change: transform;
  transition: all 0.3s ease-in-out;
  cursor: grab;

  &.moving {
    transition: none;
    cursor: grabbing;
  }
}
</style>