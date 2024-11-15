<template>
  <div>
   
    <label class="checkbox-container">
      <input type="checkbox" v-model="showEmpty" @change="fetchRubrics" />
      Отображать пустые рубрики
      <span class="custom-checkbox"></span>
    </label>


    <p>Сумма отмеченных count: {{ totalSelectedCount }}</p>


    <ul>
      <RubricNode
        v-for="rubric in rubrics"
        :key="rubric.id"
        :rubric="rubric"
        @update-selected="updateSelectedCount"
      />
    </ul>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import RubricNode from './RubricNode.vue';


const showEmpty = ref(false);
const rubrics = ref([]);


const fetchRubrics = async () => {
  const allowEmptyParam = showEmpty.value ? 1 : 0;
  const response = await fetch(`https://www.klerk.ru/yindex.php/v3/event/rubrics?allowEmpty=${allowEmptyParam}`);
  const data = await response.json();
  rubrics.value = data;
};


const selectedCounts = ref(new Set());
const totalSelectedCount = computed(() => {
  let total = 0;
  selectedCounts.value.forEach(count => {
    total += count;
  });
  return total;
});


const updateSelectedCount = (count, isSelected) => {
  if (isSelected) {
    selectedCounts.value.add(count);
  } else {
    selectedCounts.value.delete(count);
  }
};


fetchRubrics();
</script>


<style scoped>
.checkbox-container {
  display: flex;
  align-items: center;
  flex-direction: row-reverse;
  max-width:310px;
  gap: 20px;
}

</style>