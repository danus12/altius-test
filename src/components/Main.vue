<template>
  <div>
    <div>
      <button style="margin-bottom: 20px;margin-right:5px" @click="lancer2b" :disabled="lancer2bProgressing">
        Lancer les boules
      </button><br>
      <span style="margin-bottom: 10px">Nbre de collisions entre les boules : {{collisions/2}}</span>
      
    </div>
    <div class="content" :style="style">
      <Case
        v-for="index in cases"
        :key="index"
        :id="getReference(index)"
        :ref="getReference(index)"
        :width="caseWidth"
        :height="caseHeight"
      />
    </div>
    <div>
        <ul>
          <li>Pour N = 8, 4 collisions suffisent pour mettre fin à la partie</li>
          <li>Pour N = 10, 5 collisions suffisent pour mettre fin à la partie</li>
        </ul>
        
    </div>
    <p>Donc pour une matrice NxN, avec nombre de boules à vitesse <br>
     constante débutant dans le sens contraire, f(N) = N/2</p>
    <Boule
      type="bleue"
      v-show="lancerDeuxBoules"
      :case="Math.pow(this.matriceN, 2)"
      id="oneB"
      ref="oneB"
    />
    <Boule
      type="rouge"
      v-show="lancerDeuxBoules"
      :case="Math.pow(this.matriceN, 2) - this.matriceN + 1"
      id="oneR"
      ref="oneR"
    />
    <Boule
      type="rouge"
      v-show="lancerDeuxBoules && lancer4Boules"
      :case="Math.pow(this.matriceN, 2) - this.matriceN + 1"
      id="twoR"
      ref="twoR"
    />
    <Boule
      type="bleue"
      v-show="lancerDeuxBoules && lancer4Boules"
      :case="Math.pow(this.matriceN, 2)"
      id="twoB"
      ref="twoB"
    />
  </div>
</template>

<script>
import BouleSens from "@/Utils/BouleSens";
import Case from "@/components/Case.vue";
import Boule from "@/components/Boule.vue";
export default {
  components: {
    Case,
    Boule,
  },
  name: "Main",
  data() {
    return {
      width: 400,
      height: 400,
      N: 8,
      lancerDeuxBoules: true,
      firstCollision: false,
      lancer4Boules: false,
      lancer2bProgressing: false,
      boules: [],
      murNumbers: [],
      collisions:0,
      trousNumbers: [],
    };
  },

  mounted() {
    this.init();
  },
  methods: {
    initTrous(){
      this.trousNumbers.push(1)
      this.trousNumbers.push(this.N)
      this.trousNumbers.push(Math.pow(this.N,2))
      this.trousNumbers.push(this.N*(this.N-1))
    },
    generateMur() {
      var addNumberInterator = 1;
      for (let index = 1; index <= this.N; index++) {
        this.murNumbers.push(index);
      }
      const murNumbersTemp = this.murNumbers;
      murNumbersTemp.forEach((element) => {
        this.murNumbers.push(element * this.N);
      });
      for (let index = 1; index <= this.N; index++) {
        addNumberInterator = addNumberInterator + this.N;
        this.murNumbers.push(addNumberInterator);
      }
      for (let index = this.N*(this.N-1); index <= Math.pow(this.N,2); index++) {
        this.murNumbers.push(index);
      }
      console.log(this.murNumbers);
    },
    init() {
      this.generateMur();
      if (this.lancerDeuxBoules) {
        this.boules.push(this.$refs.oneB);
        this.boules.push(this.$refs.oneR);
        if (this.lancer4Boules) {
          this.boules.push(this.$refs.twoB);
          this.boules.push(this.$refs.twoR);
        }
      }
    },
    collisionAvecBoule(bouleOne, bouleTwo) {
      if (!this.firstCollision) {
        this.firstCollision = true;
        /*this.lancer4Boules = true;
        this.boules.push(this.$refs.twoB);
        this.boules.push(this.$refs.twoR);*/
      }

      if (bouleOne.caseTemporary > bouleTwo.caseTemporary) {
        switch (bouleOne.sens) {
          case BouleSens.ARRIERE:
            bouleOne.sens = BouleSens.OHDROITE;
            bouleTwo.sens = BouleSens.OHGAUCHE;
            break;
          case BouleSens.OBGAUCHE:
            bouleOne.sens = BouleSens.OBDROITE;
            bouleTwo.sens = BouleSens.OBGAUCHE;
            break;
          case BouleSens.OHGAUCHE:
            bouleOne.sens = BouleSens.OHDROITE;
            bouleTwo.sens = BouleSens.OHGAUCHE;
            break;
        }
      } else {
        switch (bouleOne.sens) {
          case BouleSens.OBDROITE:
            bouleOne.sens = BouleSens.OBGAUCHE;
            bouleTwo.sens = BouleSens.OBDROITE;
            break;
          case BouleSens.AVANT:
            bouleOne.sens = BouleSens.OHGAUCHE;
            bouleTwo.sens = BouleSens.OHDROITE;
            break;
          case BouleSens.OHDROITE:
            bouleOne.sens = BouleSens.OHGAUCHE;
            bouleTwo.sens = BouleSens.OHDROITE;
            break;
        }
      }
    },
    lancer2b() {
      if (!this.lancer2bProgressing) {
        this.lancer2bProgressing = true;
        setInterval(() => {
          this.$refs.oneB.begin();
          this.$refs.oneR.begin();
          if (this.lancer4Boules) {
            this.$refs.twoB.begin();
            this.$refs.twoR.begin();
          }
          for (let i = 0; i < this.boules.length; i++) {
            for (let j = 0; j < this.boules.length; j++) {
              if (
                this.boules[i].caseTemporary != this.boules[j].caseTemporary
              ) {
                if (
                  Math.pow(
                    this.boules[i].caseTemporary - this.boules[j].caseTemporary,
                    2
                  ) == 1
                ) {
                  this.collisions++
                  this.collisionAvecBoule(this.boules[i], this.boules[j]);
                  break;
                }
              }
            }
          }
        }, 1000);
      }
    },
    getReference(key) {
      return `case${key}`;
    },
  },
  watch: {},
  computed: {
    style() {
      return `width:${this.width}px;height:${this.height}px;grid-template-columns: repeat(${this.matriceN}, 1fr);`;
    },
    caseHeight() {
      return this.width / this.N;
    },
    caseWidth() {
      return this.height / this.N;
    },
    cases() {
      return Math.pow(this.N, 2);
    },

    matriceN: {
      get: function () {
        return this.N;
      },
      set: function (newValue) {
        if (newValue < 8) {
          this.N = 8;
        } else if (newValue > 32) {
          this.N = 32;
        } else {
          this.N = newValue;
        }
      },
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
body {
  display: flex;
  justify-content: center;
  align-content: center;
  align-items: center;
}

.content {
  background-color: white;
  display: grid;
}
</style>
