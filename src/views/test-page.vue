<template>
  <div>
    <h1>This is a test page!</h1>
    <multiselect
      v-model="results"
      :items="countries"
    />
    <p>Results: {{ results }}</p>
  </div>
</template>

<script>
import Multiselect from '@/components/multiselect.vue';

export default {
  label: 'TestPage',
  components: {
    Multiselect,
  },
  data() {
    return {
      results: [],
      countries: [
        { value: 10, label: 'Россия' },
        { value: 11, label: 'США' },
        { value: 12, label: 'Англия' },
        { value: 13, label: 'Франция' },
        { value: 14, label: 'Австрия' },
        { value: 15, label: 'Азербайджан' },
        { value: 16, label: 'Албания' },
        { value: 17, label: 'Алжир' },
        { value: 18, label: 'Ангола' },
        { value: 19, label: 'Андорра' },
        { value: 20, label: 'Антигуф и Барбуда' },
        { value: 21, label: 'Аргентина' },
        { value: 22, label: 'Армения' },
      ],
    };
  },
};
</script>
