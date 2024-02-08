<template>
  <div class="d-flex">
    <v-menu>
      <template v-slot:activator="{ props }">
        <v-btn
          class="menu-btn"
          color=""
          elevation="0"
          v-ripple="{ class: 'text-error' }"
          v-bind="props"
        >
          <img
            class="menu-img"
            src="../assets/arrow-to-down-right-svgrepo-com.svg"
            alt=""
          />
          {{ menuHeader }}
        </v-btn>
      </template>
      <v-list>
        <v-list-item
          v-for="(item, index) in props.items"
          :key="index"
          :value="index"
          @click="menuEvent(item.title)"
        >
          <v-list-item-title>{{ item.title }}</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-menu>
  </div>
</template>

<script lang="ts" setup>
import "@mdi/font/css/materialdesignicons.css";
import { ref } from "vue";

interface Item {
  title: string;
}

const emit = defineEmits(["menu-event"]);

const props = defineProps({
  items: {
    type: Array as () => Item[],
    required: true,
  },
  selected: {
    type: Number,
    required: true,
  },
});

const menuHeader = ref(
  props.selected > 0 ? `SELECTED: ${props.selected}` : "SELECT..."
);

function menuEvent(title: string) {
  emit("menu-event", title);
}
</script>

<style scoped>
.menu-btn {
  color: #b90a0a;
}
.menu-btn:hover {
  background-color: #f4ebeb;
}

.menu-img {
  max-width: 30px;
  max-height: 30px;
}
</style>
