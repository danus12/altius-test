<template>
  <div class="boule" :style="style"></div>
</template>
<script>
import BouleSens from "@/Utils/BouleSens";
export default {
  name: "Boule",
  props: {
    type: String,
    case: Number,
  },
  data() {
    return {
      caseTemporary: this.$props.case,
      alreadyCollision: false,
      inCollisionMur: false,
      sens: undefined,
      rest: true,
      sensChanging: false,
    };
  },
  methods: {
    next_position() {
      if (!this.inCollisionMur) {
        console.log("pas de collision stored");
        console.log(this.sens);
        switch (this.sens) {
          case BouleSens.ARRIERE:
            this.caseTemporary--;
            break;
          case BouleSens.AVANT:
            this.caseTemporary++;
            break;
          case BouleSens.OHGAUCHE:
            this.caseTemporary = this.caseTemporary - this.$parent.N - 1;
            break;
          case BouleSens.OHDROITE:
            this.caseTemporary = this.caseTemporary - this.$parent.N + 1;
            break;
          case BouleSens.OBDROITE:
            this.caseTemporary = this.caseTemporary + (this.$parent.N + 1);
            break;
          case BouleSens.OBGAUCHE:
            this.caseTemporary = this.caseTemporary + (this.$parent.N - 1);
            break;
        }
      } else {
        console.log("collision stored");
        this.collisionMur();
      }
    },
    collisionMur() {
      switch (this.sens) {
        case BouleSens.OHGAUCHE:
          this.caseTemporary = this.caseTemporary - this.$parent.N + 1;
          this.sens = BouleSens.OHDROITE
          break;
        case BouleSens.OHDROITE:
            this.sens = BouleSens.OHGAUCHE
          this.caseTemporary = this.caseTemporary - this.$parent.N - 1;
          break;
        case BouleSens.OBDROITE:
            this.sens = BouleSens.OBGAUCHE
          this.caseTemporary = this.caseTemporary + (this.$parent.N - 1);
          break;
        case BouleSens.OBGAUCHE:
            this.sens = BouleSens.OBDROITE
          this.caseTemporary = this.caseTemporary + (this.$parent.N + 1);
          break;
      }
      this.inCollisionMur = false;
    },
    begin() {
      this.next_position();
      this.moveTo(this.caseTemporary);
    },
    moveTo(valeur) {
      this.rest = false;
      if (valeur <= Math.pow(this.$parent.N, 2) && valeur >= 1) {
        var caseInside = this.$parent.$refs[`case${valeur}`][0];
        var caseInsideRef = caseInside.$el;
        caseInsideRef.appendChild(this.$el);
        this.sensChanging = false;
      }
    },
  },
  mounted() {
    if (this.sens == undefined && !this.alreadyCollision) {
      if (this.$props.type == "bleue") {
        this.sens = BouleSens.ARRIERE;
      } else {
        this.sens = BouleSens.AVANT;
      }
    }
    this.moveTo(this.caseTemporary);
  },
  watch: {
    sens: function (newVal, oldVal) {
      if (newVal != oldVal) {
        this.sensChanging = true;
      }
    },
    case: function (newVal, oldVal) {
      console.log(oldVal, newVal);
      this.caseTemporary = newVal;
    },
    caseTemporary: function (value, old) {
      console.log(value, old);
      if (
        !this.rest &&
        this.sens != BouleSens.ARRIERE &&
        this.sens != BouleSens.AVANT &&
        !this.sensChanging
      ) {
        if (this.$parent.murNumbers.indexOf(value) >= 0) {
          this.inCollisionMur = true;
        }
      }
    },
  },
  computed: {
    style() {
      switch (this.$props.type) {
        case "bleue":
          return "background-color: blue";
        case "rouge":
          return "background-color: red";
        default:
          return "background-color: blue";
      }
    },
  },
};
</script>

<style scoped>
.boule {
  position: absolute;
  border-radius: 50%;
  height: 30px;
  width: 30px;
  min-height: 10px;
  min-width: 10px;
}
</style>