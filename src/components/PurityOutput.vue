<template>
<div>
    <div class="flex justify-center items-center" v-show="impurities.length > 0">
        <div class="masspercentage mr-2">
            <div class="flex justify-center items-center h-100 rounded-full w-32 h-32 border border-green-700 shadow-md mr-2">
                <div class="flex items-top font-hairline text-green-600 tracking-wide">
                    <div class="text-4xl">{{ productMassFraction }}</div>

                </div>
            </div>
            <div class="text-green-600 text-center mt-2 text-lg">mass%</div>
        </div>

        <div class="molpercentage">
            <div class="flex justify-center items-center h-100 rounded-full w-24 h-24 border border-gray-600 shadow-md mr-2">
                <div class="flex items-top font-thin tracking-wide">
                    <div class="text-3xl">{{ productMolFraction }}</div>
                </div>
            </div>
            <div class="text-base text-center mt-2">mol%</div>
        </div>

    </div>

    <impurity-list @remove-impurity="removeItem" :impurities="impurities" :molmass="sumOfMolMasses" :molfrac="sumOfMolfractions" />


    <modal name="show-calculation" height="auto" :scrollable="true">
      <calculation-modal :productmass="productMass" :impurities="impurities" />
    </modal>

</div>
</template>

<script>
import CalculationModal from './CalculationModal.vue';
import ImpurityList from './ImpurityList';

export default {
  props: ['impurities', 'productMass'],

  components: {
    ImpurityList,
    CalculationModal
  },

  methods: {
    removeItem(i) {
      this.$emit('remove-impurity', i);
    }
  },

  computed: {
    sumOfMolfractions() {
      let sum = 0;

      Object.keys(this.impurities).forEach(impurity => {
        sum += this.impurities[impurity].molfrac;
      });

      return sum + 1;
    },

    sumOfMolMasses() {
      let sum = 0;

      Object.keys(this.impurities).forEach(impurity => {
        let fraction =
          this.impurities[impurity].molfrac / this.sumOfMolfractions;
        sum += fraction * this.impurities[impurity].mass;
      });

      let productMassFraction =
        Number(this.productMass) / this.sumOfMolfractions;

      return sum + productMassFraction;
    },

    productMassFraction() {
      return (
        this.productMass /
        this.sumOfMolfractions /
        this.sumOfMolMasses *
        100
      ).toFixed(1);
    },

    productMolFraction() {
      return (1 / this.sumOfMolfractions * 100).toFixed(1);
    }
  }
};
</script>

