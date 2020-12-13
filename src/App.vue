<template>
  <div class="app">
    <scroll-watcher
      v-if="false"
      class="app__scroll-watcher"
      @scroll-progress="onScrollProgress"
    />
    <sprite-sequencer
      class="app__sprite"
      :frame-sources="frameSources"
      :frame-index="frameIndex"
      v-if="false"
    />

    <!-- START VISIBLE -->

    <div
      class="app__scroller"
      ref="scroller"
      :style="{ height: scrollHeight + 'px' }"
    />

    <div class="app__stage">
      <div class="app__block">
        <div class="app__copy">
          <h1>
            This is a<br />
            <span style="font-size: 1.75em">Cloud.</span>
          </h1>
          <p>
            These hollow little pods are the key to unlocking your running
            potential.
          </p>
          <p>Let us explain...</p>
        </div>
      </div>
    </div>
    <div class="app__stage">
      <div class="app__block">
        <div class="app__copy">
          <p>
            By linking clouds together, we created a highly adaptive outsole
            that reacts to your unique running style.
          </p>
        </div>
      </div>
    </div>

    <div class="app__stage">
      <div class="app__block">
        <div class="app__copy">
          <h2>
            This is<br />
            <span style="font-size: 1.33em">Cloudtec.</span>
          </h2>
          <p>
            Strategically positioned cloud elements cushion as they compress
            horizontally and vertically.
          </p>
          <p>
            As we all land slightly differently, the individual cloud elements
            respond slightly differently too.
          </p>
          <p>
            Cushioning like you’ve never experienced and a completely new
            running sensation. We call it running on clouds.
          </p>
          <p>Heres how it works...</p>
        </div>
      </div>
    </div>

    <div class="app__stage">
      <div class="app__block">
        <div class="app__copy">
          <h2>Soft Landings</h2>
          <p>
            Impact protection meets energy return. CloudTec® only cushions
            during landings – which is exactly where you need it.
          </p>
        </div>
      </div>
    </div>

    <div class="app__stage">
      <div class="app__block">
        <div class="app__copy">
          <h2>Explosive Takeoffs</h2>
          <p>
            Every time your foot strikes the ground, the Cloud elements
            compress. Creating a firm and flat foundation for natural, powerful
            take-offs that feel effortless.
          </p>
        </div>
      </div>
    </div>

    <div class="app__stage">
      <div class="app__block">
        <div class="app__copy">
          <h2>Run on Clouds®.</h2>
          <p>
            Every time your foot strikes the ground, the Cloud elements
            compress. Creating a firm and flat foundation for natural, powerful
            take-offs that feel effortless.
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import sprite from "../public/sprite/sprite.json";
import SpriteSequencer from "./components/SpriteSequencer.vue";
import ScrollWatcher from "./components/ScrollWatcher.vue";

// import gsap from "./utils/gsap";
// import ScrollTrigger from "gsap/dist/ScrollTrigger";
// import CustomEase from "./utils/gsap/CustomEase.js";
// import SlowMo from "./utils/gsap/EasePack.js";

import { gsap } from "gsap";
import { SlowMo } from "gsap/EasePack";
import { ScrollTrigger } from "gsap/ScrollTrigger";

function supportsWebp() {
  var c = document.createElement("canvas");
  if (c.getContext && c.getContext("2d")) {
    return c.toDataURL("image/webp").indexOf("data:image/webp") == 0;
  } else {
    return false;
  }
}

export default {
  name: "App",
  components: {
    SpriteSequencer,
    ScrollWatcher,
  },
  data() {
    return {
      progress: 0,
      frameProgress: 0,
      scrollHeight: 0,
    };
  },
  computed: {
    frameSources() {
      return supportsWebp ? sprite.webp : sprite.png;
    },
    frameIndex() {
      return Math.round(this.frameProgress * (this.frameSources.length - 1));
    },
  },
  mounted() {
    console.clear();
    gsap.registerPlugin(ScrollTrigger, SlowMo);
    this.updateTimeline();
  },
  methods: {
    updateTimeline() {
      let t = new gsap.timeline({
        scrollTrigger: {
          trigger: this.$refs.scroller,
          start: "top top",
          end: "bottom bottom",

          scrub: true,
          //markers: true,
        },
      });
      let scrollHeight = 0;
      let blockPadding = this.$el.querySelector(".app__stage").offsetHeight;
      blockPadding = 400;

      this.$el.querySelectorAll(".app__stage").forEach((stage) => {
        scrollHeight +=
          stage.querySelector(".app__block").offsetHeight + blockPadding;
        this.animateBlockCopy(stage, blockPadding, t);
      });

      this.scrollHeight = t.duration() * 1.5;
      this.timeline = t;
    },
    animateBlockCopy(stage, blockPadding, timeline) {
      let block = stage.querySelector(".app__block");
      let blockCopy = stage.querySelector(".app__block .app__copy");
      let children = [...blockCopy.children];
      let childrenHeight = children.reduce(
        (acc, el) => acc + el.offsetHeight,
        0
      );

      //childrenHeight = block.offsetHeight;
      //console.log(childrenHeight);
      //let tweens = [];

      let p = 400;
      const pause = 0;
      const pauseTime = 0;
      let y = childrenHeight + pause;

      // // ---------- ANIMATE BLOCK
      // timeline.fromTo(
      //   block,
      //   {
      //     y: y + p,
      //     autoAlpha: 0,
      //     filter: "blur(10px)",
      //   },
      //   {
      //     y: y,
      //     autoAlpha: 1,
      //     ease: "none",
      //     duration: p,
      //     filter: "blur(0px)",
      //   }
      // );

      // timeline.to(block, {
      //   y: 0,
      //   ease: "slow(0.1, 0.6, false)",
      //   duration: y,
      // });

      // timeline.to(block, {
      //   y: -p,
      //   autoAlpha: 0,
      //   ease: "none",
      //   duration: p,
      // });
      // // ----------

      timeline.add([
        gsap.fromTo(
          block,
          {
            y: y + p,
            autoAlpha: 0,
          },
          {
            y: y,
            autoAlpha: 1,
            ease: "none",
            duration: 1,
          }
        ),
      ]);
      children.forEach((child, index) => {
        y -= child.offsetHeight;
        timeline.add([
          // scroll block
          gsap.to(block, {
            y: y,
            ease: "none",
            duration: child.offsetHeight * 1.5,
          }),

          // child entrance
          gsap.fromTo(
            child,
            { y: 120, autoAlpha: 0 },
            {
              y: 0,
              autoAlpha: 1,
              duration: Math.min(child.offsetHeight * 1.5, 300),

              ease: "sine.out",
            }
          ),
        ]);
      });
      //timeline.to(block, { y: y - pause, ease: "none", duration: pauseTime });

      timeline.add([
        gsap.to(block, {
          y: y - p * 1.5,
          ease: "none",
          duration: p * 2,
        }),
        gsap.to(block, {
          ease: "none",
          autoAlpha: 0,
          filter: "blur(10px)",
          delay: p,
          duration: p,
        }),
      ]);

      // this is just a delay before the next one
      timeline.to(block, {
        y: y - p * 2,
        duration: p,
      });

      y -= p;

      // timeline.to(child, {
      //   y: y,
      //   autoAlpha: 0,
      // });

      // timeline.fromTo(
      //   block,
      //   { y: childrenHeight + p, autoAlpha: 0 },
      //   {
      //     duration: p / 100,
      //     y: childrenHeight,
      //     autoAlpha: 1,
      //     ease: "none",
      //   }
      // );
      // timeline.to(block, {
      //   y: 0,
      //   duration: childrenHeight / 100,
      //   ease: "none",
      // });
      // timeline.to(block, {
      //   y: "-=" + p,
      //   filter: "blur(10px)",
      //   autoAlpha: 0,
      //   duration: p / 100,
      //   ease: "none",
      // });

      //timeline.add(tweens);

      // children.forEach((el, idx) => {
      //   let dur = 0.5;
      //   let tweens = [];

      //   // move the whole block up
      //   tweens.push(
      //     gsap.fromTo(
      //       blockCopy,
      //       {
      //         y: childrenHeight,
      //       },
      //       {
      //         y: childrenHeight - el.offsetHeight,
      //         duration: dur,
      //         ease: "power3.inOut",
      //       }
      //     )
      //   );

      // animate each block in
      // tweens.push(
      //   gsap.fromTo(
      //     el,
      //     {
      //       y: 150,
      //       autoAlpha: 0,
      //     },
      //     {
      //       y: 0,
      //       autoAlpha: 1,
      //       duration: dur * 0.95,
      //       delay: dur * 0.05,
      //       ease: "power3.inOut",
      //     }
      //   )
      // );
      //timeline.add(tweens);
      //timeline.add(gsap.to(blockCopy,{y: blockPadding * -1, duration: dur})

      // animate the full block up on element height at a time
      // tweens.push(
      //   gsap.fromTo(
      //     blockCopy,
      //     {
      //       y: childrenHeight,
      //     },
      //     {
      //       y: childrenHeight - el.offsetHeight,
      //       duration: dur,
      //       ease: "power3.inOut",
      //     }
      //   )
      // );

      // // animate each block in
      // tweens.push(
      //   gsap.fromTo(
      //     el,
      //     {
      //       y: 150,
      //       autoAlpha: 0,
      //     },
      //     {
      //       y: 0,
      //       autoAlpha: 1,
      //       duration: dur * 0.95,
      //       delay: dur * 0.05,
      //       ease: "power3.inOut",
      //     }
      //   )
      // );
      // timeline.add(tweens);
      //timeline.add(gsap.to(block, {}));

      //childrenHeight -= el.offsetHeight;
      //});

      //console.log(childrenHeight);

      // timeline.add(
      //   gsap.fromTo
      // )
    },
  },
};
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Manrope:wght@500;600;700;800&display=swap");
body,
html {
  margin: 0;
  padding: 0;
  background-color: #65a7ca;
}
.app {
  font-family: Helvetica, Arial, sans-serif;
  color: white;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;

  &__scroller {
  }

  // /test

  &__stage {
    position: fixed;
    box-sizing: border-box;
    width: 100vw;
    height: 100vh;
    top: 0;
    left: 0;
    padding-left: 20%;
    box-sizing: border-box;
  }

  &__block {
    position: fixed;
    bottom: 30%;
    //border: 1px solid blue;
  }
  &__copy {
    /* border: 1px dashed rgba(white, 0.5);
    border-bottom: 2px solid white; */

    font-weight: bold;
    text-align: left;
    font-size: 40px;
    max-width: 12em;
    letter-spacing: -0.03em;

    > * {
      transform-origin: top left;
      margin: 0;
      padding-top: 0;

      // use padding so it gets included in offsetHeight
      padding-bottom: 40px;
    }

    h1,
    h2,
    h3 {
      font-size: 62px;
      text-align: left;
      line-height: 1;
      font-weight: 800;
    }

    p {
      font-weight: 670;
    }
  }

  &__scroll-watcher {
    height: 30000px;
  }

  &__scroll-progress {
    color: white;
    position: fixed;
    top: 10px;
    left: 10px;
    z-index: 1;
    font-size: 12px;
  }
}
/* 
&__sprite {
  position: fixed;
  left: 0;
  top: 0;
  width: 100vw;
  height: 100vh;
  //filter: blur(10px) grayscale(1) brightness(50);
  filter: blur(10px);
  //opacity: 0.5;
} */
</style>
