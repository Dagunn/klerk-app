<template>
  <li>
    <div class="rubric-node">
      <label class="checkbox-container">
        <input type="checkbox" @change="toggleSelected" />
        <span class="custom-checkbox"></span>
      </label>

      <button class="expand-button" @click="toggleExpand">
        {{ isExpanded ? '−' : '+' }}
      </button>

  
      <a :href="`https://www.klerk.ru${rubric.url}`" target="_blank">
        {{ rubric.title }}
      </a>

      
      ({{ rubric.count }} / {{ totalCount }} / {{ childCount }})


      <ul v-if="isExpanded && hasChildren">
        <RubricNode
          v-for="child in rubric.children"
          :key="child.id"
          :rubric="child"
          @update-selected="handleChildSelection"
        />
      </ul>
    </div>
  </li>
</template>

<script setup>
import { ref, computed } from 'vue';
import RubricNode from './RubricNode.vue';

const props = defineProps({
  rubric: {
    type: Object,
    required: true
  }
});

const isExpanded = ref(false);
const isSelected = ref(false);

const hasChildren = computed(() => Array.isArray(props.rubric.children) && props.rubric.children.length > 0);


const totalCount = computed(() => {
  const recursiveSum = (node) => {
    let sum = node.count || 0;
    if (node.children) {
      for (const child of node.children) {
        sum += recursiveSum(child);
      }
    }
    return sum;
  };
  return recursiveSum(props.rubric);
});


const childCount = computed(() => {
  return hasChildren.value ? props.rubric.children.length : 0;
});


const emit = defineEmits(["update-selected"]);
const toggleSelected = () => {
  isSelected.value = !isSelected.value;
  emit("update-selected", totalCount.value, isSelected.value);
};


const toggleExpand = () => {
  isExpanded.value = !isExpanded.value;
};


const handleChildSelection = (childCount, isSelected) => {
  emit("update-selected", childCount, isSelected);
};
</script>