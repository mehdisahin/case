<template>
  <v-container class="flex-center">
    <v-card class="table align-center" title="Cost Centres">
      <Filter
        @filter-search-event="
          (filterSearch) => handleFilterSearchEvent(filterSearch)
        "
        @filter-checkbox-event="(filterCheckboxes: FilterCheckboxes) => handleFilterCheckboxEvent(filterCheckboxes)"
      />
      <v-table class="v-table">
        <thead>
          <tr class="table-head">
            <th>
              Cost Centre

              <Menu
                :items="items"
                :selected="selected"
                @menuEvent="(title: string) => handleMenuEvent(rows, title)"
              />
            </th>
            <th>
              Provider
              <v-checkbox
                color="red"
                value="true"
                :model-value="filterCheckbox"
                @click="
                  () => {
                    checkFilterCheckbox('provider');
                  }
                "
                indeterminate
                hide-details
              ></v-checkbox>
            </th>
            <th>
              Consumer
              <v-checkbox
                color="red"
                value="true"
                :model-value="filterCheckbox"
                @click="
                  () => {
                    checkFilterCheckbox('consumer');
                  }
                "
                indeterminate
                hide-details
              ></v-checkbox>
            </th>
            <th>
              On Account
              <v-text-field
                class="on-account-text-field"
                variant="underlined"
              ></v-text-field>
            </th>
            <th class="img-cell">
              <v-btn
                elevation="0"
                class="table-img"
                v-ripple="{ class: 'text-error' }"
                @click="checkCheckbox(rows, filterCheckbox)"
              >
                <img src="../assets/menu-ui-svgrepo-com.svg" alt="" />
              </v-btn>
            </th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="(row, index) in rows" :key="index" class="tabe-body-row">
            <td
              v-if="row.isVisible"
              class="a d-flex align-center justfy-center"
              colspan="2"
            >
              <v-checkbox
                class="first-checkbox"
                color="red"
                :model-value="row.selected"
                @click="
                  () => {
                    row.selected = !row.selected;

                    if (row.selected) {
                      numSelected++;
                    } else {
                      numSelected--;
                    }
                  }
                "
                hide-details
              ></v-checkbox>
              <p>{{ row.name }}</p>
            </td>
            <td v-if="row.isVisible">
              <v-checkbox
                color="red"
                :model-value="row.provider"
                @click="toggleCheckbox('provider', row)"
                hide-details
              ></v-checkbox>
            </td>
            <td v-if="row.isVisible">
              <v-checkbox
                color="red"
                :model-value="row.consumer"
                @click="toggleCheckbox('consumer', row)"
                hide-details
              ></v-checkbox>
            </td>
            <td v-if="row.isVisible">
              <v-text-field
                class="on-account-text-field"
                :model-value="row.onAccount"
                variant="underlined"
              ></v-text-field>
            </td>
          </tr>
        </tbody>
      </v-table>

      <div class="add-row">
        <v-text-field
          class="add-row-text-field"
          :model-value="newRow.name"
          placeholder="Add New Row"
          variant="underlined"
          @input="(event: any) => {
            (newRow.name = event.target.value);
          }"
        ></v-text-field>

        <v-btn
          class="add-row-btn"
          color="success"
          @click="() => addNewRow(newRow)"
        >
          <v-icon icon="mdi-table-row-plus-after"></v-icon>
        </v-btn>
      </div>
    </v-card>
  </v-container>
</template>

<script lang="ts" setup>
import { reactive, ref } from "vue";
import Menu from "./Menu.vue";
import Filter from "./Filter.vue";
import { watch } from "vue";

interface TableProps {
  title: string;
}

interface Item {
  title: string;
}

interface Row {
  selected: boolean;
  isVisible: boolean;
  name: string;
  provider: boolean;
  consumer: boolean;
  onAccount: number;
}

interface FilterCheckboxes {
  isConsumer: boolean;
  isProvider: boolean;
}

const numSelected = ref(0);

let selected = numSelected.value;

const items: Item[] = [
  { title: "Visible: Select" },
  { title: "Visible: Unselect" },
  { title: "Visible: Invert" },
  { title: "All: Select" },
  { title: "All: Unselect" },
  { title: "All: Invert" },
];

const filterCheckbox: string[] = reactive([]);

const newRow: Row = reactive({
  selected: false,
  isVisible: true,
  name: "",
  provider: false,
  consumer: false,
  onAccount: 0,
});

const rows: Row[] = reactive([
  {
    selected: false,
    isVisible: true,
    name: "Apportionment 1",
    provider: false,
    consumer: false,
    onAccount: 0,
  },
  {
    selected: false,
    isVisible: true,
    name: "Apportionment 2",
    provider: false,
    consumer: false,
    onAccount: 0,
  },
  {
    selected: false,
    isVisible: true,
    name: "Consumer 1",
    provider: false,
    consumer: false,
    onAccount: 0,
  },
  {
    selected: false,
    isVisible: true,
    name: "Consumer 2",
    provider: false,
    consumer: false,
    onAccount: 0,
  },
  {
    selected: false,
    isVisible: true,
    name: "Provider 1",
    provider: false,
    consumer: false,
    onAccount: 0,
  },
  {
    selected: false,
    isVisible: true,
    name: "Provider 2",
    provider: false,
    consumer: false,
    onAccount: 0,
  },
]);

// SET ELEMENTS TO THE LOCAL STORAGE
watch(rows, () => {
  localStorage.setItem("rows", JSON.stringify(rows));
});

function addNewRow(newRow: Row) {
  rows.push({ ...newRow });

  newRow.name = "";
}

function handleFilterSearchEvent(filterSearch: string) {
  const { filterSearch: filterSearchValue } = filterSearch as unknown as {
    filterSearch: string;
  };
  rows.forEach((row) => {
    if (row.name === "") {
      row.isVisible = true;
    }
    if (row.name.toLowerCase().includes(filterSearchValue.toLowerCase())) {
      row.isVisible = true;
    } else {
      row.isVisible = false;
    }
  });

  rows.filter((row) => !row.isVisible);
}

function handleFilterCheckboxEvent(filterCheckboxes: FilterCheckboxes) {
  rows.forEach((row) => {
    if (!filterCheckboxes.isConsumer && !filterCheckboxes.isProvider) {
      row.isVisible = true;
    } else if (filterCheckboxes.isConsumer && row.consumer) {
      row.isVisible = true;
    } else if (filterCheckboxes.isProvider && row.provider) {
      row.isVisible = true;
    } else {
      row.isVisible = false;
    }
  });
}

function checkCheckbox(rows: Row[], filterCheckbox: string[]): void {
  const filterCheckboxEls = filterCheckbox.map((el) => el.toLowerCase());

  rows.forEach((row) => {
    if (filterCheckboxEls.includes("provider") && row.selected) {
      row.provider = true;
    }
    if (filterCheckboxEls.includes("consumer") && row.selected) {
      row.consumer = true;
    }
  });
}

function checkFilterCheckbox(filterCheckboxEl: string) {
  if (filterCheckbox.includes(filterCheckboxEl)) {
    filterCheckbox.splice(filterCheckbox.indexOf(filterCheckboxEl), 1);
  } else {
    filterCheckbox.push(filterCheckboxEl);
  }
}

function toggleCheckbox(name: string, row: any) {
  row[name] = !row[name];
}

function handleMenuEvent(rows: Row[], selectionOption: string): Row[] {
  type SelectionType = "Select" | "Unselect" | "Invert";
  type SelectionVis = "Visible" | "All";

  const selectionVisiblity: SelectionVis = selectionOption
    .split(":")[0]
    .trim() as SelectionVis;

  const selectionType: SelectionType = selectionOption
    .split(":")[1]
    .trim() as SelectionType;

  if (selectionVisiblity === "Visible") {
    if (selectionType === "Select") {
      rows.forEach((row) => {
        if (row.isVisible) {
          row.selected = true;
        }
      });
    } else if (selectionType === "Unselect") {
      rows.forEach((row) => {
        if (row.isVisible) {
          row.selected = false;
        }
      });
    } else if (selectionType === "Invert") {
      rows.forEach((row) => {
        if (row.isVisible) {
          row.selected = !row.selected;
        }
      });
    }
  } else if (selectionVisiblity === "All") {
    if (selectionType === "Select") {
      rows.forEach((row) => {
        row.selected = true;
      });
    } else if (selectionType === "Unselect") {
      rows.forEach((row) => {
        row.selected = false;
      });
    } else if (selectionType === "Invert") {
      rows.forEach((row) => {
        row.selected = !row.selected;
      });
    }
  } else if (selectionVisiblity === "All") {
    if (selectionType === "Select") {
      rows.forEach((row) => {
        row.selected = true;
      });
    } else if (selectionType === "Unselect") {
      rows.forEach((row) => {
        row.selected = false;
      });
    } else if (selectionType === "Invert") {
      rows.forEach((row) => {
        row.selected = !row.selected;
      });
    }
  }

  return rows;
}
</script>

<style scoped>
.flex-center {
  display: flex;
  align-items: center;
  justify-content: center;
}
.table {
  width: 90%;
}

.a {
  width: 100%;
  height: 75px !important;
}

.first-checkbox {
  /* width: 30px !important; */

  flex: 0 0 30%;
}

:deep(.on-account-text-field input) {
  text-align: right !important;
}

.img-cell {
  max-width: 60px;
}

.table-img {
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 0 auto;
  padding: 10px 15px;
  width: 40%;
  height: 50px !important;
  transition: all 0.3s;
}

.table-img img {
  min-width: 30px;
  min-height: 30px;
  /* max-width: 100px;
  max-height: 100px; */
}

.table-img:hover {
  background-color: #f4ebeb;
  border-radius: 3px;
}

.add-row {
  display: flex;
  justify-content: flex-start;
  align-items: center;
  gap: 30px;
  margin-top: 30px;
  padding: 0 30px;
}

.add-row-text-field {
  flex: 0 0 40%;
}

.add-row-btn {
  width: 50px;
  height: 50px;
  background-color: #f4ebeb;
  transition: all 0.3s;
}
</style>
