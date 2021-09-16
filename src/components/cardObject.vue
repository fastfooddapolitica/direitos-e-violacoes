<template>
  <div class="flip-container" :class="{ flip: flipped }" ref="cardRoot">
    <div class="flipper">
      <div class="front">
        <div class="card x-font">
          <span>{{ cardData.name }}</span>
        </div>
      </div>
      <div v-show="flipped" class="back">
        <div class="card card-back x-font">
          <button class="btn show-details" @click="showDetails">?</button>
          <span>{{ cardData.name }}</span>
          <span v-if="exists" class="card-year">{{ cardData.year }}</span>
          <span v-else class="card-year card-unexists">não existe</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import cardDetails from "@/components/cardDetails.vue";
import { scrollIntoView } from "@/utils";

export default {
  name: "cardObject",
  props: {
    cardData: Object,
  },
  data() {
    return {
      flipped: false,
      exists: this.cardData.year !== "x",
    };
  },
  methods: {
    flip() {
      this.flipped = true;
      this.$audio.play("release");
    },
    unflip() {
      this.flipped = false;
      this.$audio.play("release");
    },
    scrollIntoView() {
      scrollIntoView(this.$refs.cardRoot);
    },
    showDetails() {
      this.$matomo.trackEvent("jogo", "viu detalhes", this.cardData.name);
      this.$emit("openModal", {
        component: cardDetails,
        props: { cardData: this.cardData },
      });
    },
  },
};
</script>

<style scoped lang="scss">
@import "@/vars.scss";

.show-details {
  position: absolute;
  top: 3px;
  right: 3px;
  margin: 0;
  width: auto;
  border-radius: 200px;
  padding: 0.2em 0.5em;
}
.card {
  width: $card-size;
  height: $card-size;
  background-color: $sec-color;
  overflow: hidden;
  cursor: grab;
  position: relative;
  background-size: 100%;
  border-radius: 10px;
  color: $pri-color;
  padding: 10px;
  background-repeat: no-repeat;
  box-sizing: border-box;
  box-shadow: 2px 2px 4px 0px #000;
  font-size: 12pt;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
.card-year {
  background-color: $pri-color;
  padding: 4px 12px;
  font-size: 14pt;
  margin-top: 0.5rem;
  border-radius: 100px;
  color: $sec-color;
}
.card-unexists {
  font-size: 12pt;
}
.card-back {
  background-color: #454e7c;
  font-size: 10pt;
}
span {
  pointer-events: none;
  user-select: none;
}

/* Based on: https://davidwalsh.name/css-flip */
/* Look there for IE support */
/* entire container, keeps perspective */
.flip-container {
  perspective: 1000px;
  display: inline-block;
}
/* flip the pane when hovered */
/* .flip-container:hover .flipper, .flip-container.hover .flipper,  */
.flip-container.flip .flipper {
  transform: rotateY(180deg);
}

.flip-container {
  margin: 10px;
}
.flip-container,
.front,
.back {
  width: $card-size;
  height: $card-size;
  z-index: 2;
}

/* flip speed goes here */
.flipper {
  transition: 0.6s;
  transform-style: preserve-3d;
  position: relative;
}

/* hide back of pane during swap */
.front,
.back {
  backface-visibility: hidden;
  position: absolute;
  top: 0;
  left: 0;
}

/* front pane, placed above back */
.front {
  z-index: 2;
  /* for firefox 31 */
  transform: rotateY(0deg);
}

/* back, initially hidden pane */
.back {
  transform: rotateY(180deg);
}
</style>
