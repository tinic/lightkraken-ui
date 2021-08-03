<template>
  <div>
    <div class="color-picker">
      <div
        class="color-picker__overlay"
        v-if="isVisible"
        @click="hide"
      />
      <transition name="pop">
        <div
          class="color-picker__flyout"
          v-if="isVisible"
        >
          <div
            class="color-chip"
            :style="{'background': color}"
          >
            <div class="color-chip__inner">
              <div class="color-chip__title">
                HSL
              </div>
              <div class="color-chip__subtitle">
                {{ hslColorString }}
              </div>
              <div class="color-chip__title">
                RGB
              </div>
              <div class="color-chip__subtitle">
                {{ rgbColorString }}
              </div>
            </div>
          </div>
          <div class="color-picker__inner">
            <div
              class="control"
              :style="gradientH"
            >
              <input
                type="range"
                min="0"
                max="360"
                v-model="h"
                @change="emitColor()"
              >
            </div>
            <div
              class="control"
              :style="gradientS"
            >
              <input
                type="range"
                min="0"
                max="100"
                v-model="s"
                @change="emitColor()"
              >
            </div>
            <div
              class="control"
              :style="gradientL"
            >
              <input
                type="range"
                min="0"
                max="100"
                v-model="l"
                @change="emitColor()"
              >
            </div>
          </div>
        </div>
      </transition>
      <div
        class="swatch"
        :style="{'background': color}"
        @click="toggle"
      />
    </div>
    <div
      class="spacer"
      style="clear: both;"
    />
  </div>
</template>

<script>
export default {
  name: "ColorSwatch",
  props: {
    change : {
      type:Function,
      default: function() { return "" }
    },
    initial : {
      type:Function,
      default: function() { return "" }
    },
  },
  data: function() { 
    return {
      isVisible: true,
      h: 0,
      s: 0,
      l: 0
    }
  },
  computed: {
    color: function() {
      var hsl = this.hsb2hsl(parseFloat(this.h) / 360, parseFloat(this.s) / 100, parseFloat(this.l) / 100)
      var c = hsl.h + ", " + hsl.s + "%, " + hsl.l + "%";
      var s = "hsl(" + c + ")";
      return s;
    },
    hslColorString: function() {
      var c = this.h + ", " + this.s + "%, " + this.l + "%"
      return c;
    },
    rgbColorString: function() {
      var rgb = this.hsv2rgb(parseFloat(this.h) / 360, parseFloat(this.s) / 100, parseFloat(this.l) / 100);
      var c = rgb.r + ", " + rgb.g + ", " + rgb.b + ""
      return c;
    },
    gradientH: function() {
      var stops = [];
      for (var i = 0; i < 7; i++) {
        var h = i * 60;
        var hsl = this.hsb2hsl(parseFloat(h / 360), parseFloat(this.s) / 100, parseFloat(this.l / 100))
        var c = hsl.h + ", " + hsl.s + "%, " + hsl.l + "%"
        stops.push("hsl(" + c + ")")
      }

      return {
        backgroundImage: "linear-gradient(to right, " + stops.join(', ') + ")"
      }
    },
    gradientS: function() {
      var stops = [];
      var c;
      var hsl = this.hsb2hsl(parseFloat(this.h / 360), 0, parseFloat(this.l / 100))
      c = hsl.h + ", " + hsl.s + "%, " + hsl.l + "%"
      stops.push("hsl(" + c + ")")
      hsl = this.hsb2hsl(parseFloat(this.h / 360), 1, parseFloat(this.l / 100))
      c = hsl.h + ", " + hsl.s + "%, " + hsl.l + "%"
      stops.push("hsl(" + c + ")")
      return {
        backgroundImage: "linear-gradient(to right, " + stops.join(', ') + ")"
      }
    },
    gradientL: function() {
      var stops = [];
      var c;
      var hsl = this.hsb2hsl(parseFloat(this.h / 360), 0, 0)
      c = hsl.h + ", " + hsl.s + "%, " + hsl.l + "%"
      stops.push("hsl(" + c + ")")
      hsl = this.hsb2hsl(parseFloat(this.h / 360), parseFloat(this.s / 100), 1)
      c = hsl.h + ", " + hsl.s + "%, " + hsl.l + "%"
      stops.push("hsl(" + c + ")")
      return {
        backgroundImage: "linear-gradient(to right, " + stops.join(', ') + ")"
      }
    }
  },
  methods: {
    emitColor() {
      this.change(this.hsv2rgb(parseFloat(this.h) / 360, parseFloat(this.s) / 100, parseFloat(this.l) / 100));
    },
    rgb2hsv : function(r, g, b) {
      if (arguments.length === 1) {
        g = r.g, b = r.b, r = r.r;
      }
      var max = Math.max(r, g, b), min = Math.min(r, g, b),
      d = max - min,
      h,
      s = (max === 0 ? 0 : d / max),
      v = max / 255;
      switch (max) {
        case min: h = 0; break;
        case r: h = (g - b) + d * (g < b ? 6: 0); h /= 6 * d; break;
        case g: h = (b - r) + d * 2; h /= 6 * d; break;
        case b: h = (r - g) + d * 4; h /= 6 * d; break;
      }
      return {
        h: h,
        s: s,
        v: v
      }
    },
    hsv2rgb: function(h, s, v) {
      var r, g, b, i, f, p, q, t;
      if (arguments.length === 1) {
          s = h.s, v = h.v, h = h.h;
      }
      i = Math.floor(h * 6);
      f = h * 6 - i;
      p = v * (1 - s);
      q = v * (1 - f * s);
      t = v * (1 - (1 - f) * s);
      switch (i % 6) {
          case 0: r = v, g = t, b = p; break;
          case 1: r = q, g = v, b = p; break;
          case 2: r = p, g = v, b = t; break;
          case 3: r = p, g = q, b = v; break;
          case 4: r = t, g = p, b = v; break;
          case 5: r = v, g = p, b = q; break;
      }
      return {
          r: Math.round(r * 255),
          g: Math.round(g * 255),
          b: Math.round(b * 255)
      };
    },
    hsb2hsl: function(h, s, b) {
      var hsl = { h: h };
      hsl.l = (2 - s) * b;
      hsl.s = s * b;

      if (hsl.l <= 1 && hsl.l > 0) {
          hsl.s /= hsl.l;
      } else {
          hsl.s /= 2 - hsl.l;
      }

      hsl.l /= 2;

      if (hsl.s > 1) {
          hsl.s = 1;
      }
    
      if (!hsl.s > 0) hsl.s = 0


      hsl.h *= 360;
      hsl.s *= 100;
      hsl.l *= 100;

      return hsl;
    },
    show: function() {
      this.isVisible = true;
    },
    hide: function() {
      this.isVisible = false;
    },
    toggle: function() {
      this.isVisible = !this.isVisible;
    }
  },
  mounted: function () {
    var rgb = this.initial();
    var hsv = this.rgb2hsv(rgb.r, rgb.g, rgb.b);
    this.h = hsv.h * 360;
    this.s = hsv.s * 100;
    this.l = hsv.v * 100;
    this.hide();
  }
};
</script>

<style scoped>
.color-picker {
  position: absolute;
}

.color-picker__overlay {
  width: 100%;
  height: 100vh;
  position: fixed;
  top: 0px;
  left: 0px;
  background: black;
  z-index: 1;
  opacity: 0;
}

.color-picker__flyout {
  float:left;
  width: 220px;
  border: 1px solid #eee;
  border-radius: 4px;
  background: white;
  box-shadow: 0px 3px 7px rgba(0, 0, 0, 0.12);
  position: absolute;
  z-index: 2;
  top: 40px;
}

.color-picker__inner {
  padding: 1.5rem 1rem;
}

.color-chip {
  display: flex;
  justify-content: center;
  align-items: center;
  color: white;
  border-radius: 4px 4px 0 0;
}

.color-chip__inner {
  text-align: center;
}

.color-chip__subtitle {
  margin-top: 0.5rem;
  opacity: 0.7;
  font-weight: normal;
  font-size: 15px;
  text-shadow: 0px 1px 2px rgba(0, 0, 0, 0.5);
  margin: 2px;
  padding-bottom: 10px;
}

.color-chip__title {
  color: white;
  margin: 0;
  font-size: 50px;
  letter-spacing: 4px;
  text-shadow: 0px 1px 2px rgba(0, 0, 0, 0.5);
  margin: 2px;
  font-weight : 600;
}

.control {
  width: 100%;
  height: 12px;
  border-radius: 12px;
  border: 1px solid #ddd;
}

.control + .control {
  margin-top: 1rem;
}

.control input {
  width: 100%;
  margin: 0;
}

.control input[type=range] {
  -webkit-appearance: none;
  width: 100%;
  background: transparent;
}

.control input[type=range]:focus {
  outline: none;
}

.control input[type=range]::-ms-track {
  width: 100%;
  cursor: pointer;
  background: transparent;
  border-color: transparent;
  color: transparent;
}

.control input[type=range]::-webkit-slider-thumb {
  -webkit-appearance: none;
  border: 1px solid #ddd;
  height: 20px;
  width: 20px;
  border-radius: 50px;
  background: #ffffff;
  cursor: pointer;
  box-shadow: 0px 1px 2px rgba(0, 0, 0, 0.12);
  margin-top: -4px;
}

.swatch {
  float: left;
  width: 24px;
  height: 24px;
  border: 4px solid white;
  box-shadow: 0px 0px 1px rgba(0, 0, 0, 0.3);
  cursor: pointer;
}

.swatch:hover {
  border: 4px solid white;
  opacity: 0.8;
  box-shadow: 0px 0px 1px rgba(0, 0, 0, 0.3);
}

.pop-enter-active,
.pop-leave-active {
  transition: transform .5s, opacity .5s;
  transition-timing-function: cubic-bezier(.8, .3, 0.25, 1.75);
  transform: scale(1);
}

.pop-enter,
.pop-leave-active {
  opacity: 0;
  transform: scale(0);
}
</style>