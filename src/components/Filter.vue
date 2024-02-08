<template>
  <div class="filter-menu">
    <v-text-field
      class="filter-text-field"
      placeholder="Filter..."
      variant="underlined"
      :model-value="filterSearch"
      @input="(e: Event) => filterSearchEvent(e)"
    ></v-text-field>

    <v-checkbox
      id="isConsumer"
      class="filter-checkbox"
      color="red"
      label="Is Consumer"
      :model-value="filterCheckboxes.isConsumer"
      @click="(e: MouseEvent)=>filterCheckboxEvent(e)"
      hide-details
    ></v-checkbox>
    <v-checkbox
      id="isProvider"
      class="filter-checkbox"
      color="red"
      label="Is Provider"
      :model-value="filterCheckboxes.isProvider"
      @click="(e: MouseEvent)=>filterCheckboxEvent(e)"
      hide-details
    ></v-checkbox>

    <v-btn class="filter-btn" @click="resetFilters">
      <v-icon icon="mdi-filter-remove-outline"></v-icon>
    </v-btn>
  </div>
</template>

<script setup lang="ts">
import { reactive } from "vue";
import { ref } from "vue";

const emit = defineEmits(["filter-search-event", "filter-checkbox-event"]);

const filterSearch = ref("");
const filterCheckboxes = reactive({
  isConsumer: false,
  isProvider: false,
});

function resetFilters() {
  filterSearch.value = "";
  filterCheckboxes.isConsumer = false;
  filterCheckboxes.isProvider = false;
}

function filterSearchEvent(e: Event) {
  filterSearch.value = (e.target as HTMLInputElement).value.trim();

  emit("filter-search-event", {
    filterSearch: filterSearch.value,
  });
}

function filterCheckboxEvent(e: MouseEvent) {
  const target = (e.target as HTMLInputElement).id;

  filterCheckboxes[target as keyof typeof filterCheckboxes] =
    !filterCheckboxes[target as keyof typeof filterCheckboxes];

  emit("filter-checkbox-event", filterCheckboxes);
}
</script>

<style scoped>
.filter-menu {
  display: flex;
  justify-content: flex-start;
  align-items: center;
  gap: 30px;
  width: 90%;
  margin: 0;
  padding: 0 15px;
}

.filter-text-field {
  flex: 0 0 30%;
}

.filter-checkbox {
  flex: 0 0 25%;
}

.filter-btn {
  margin-bottom: 15px;
}
</style>
