<template>
  <div class="q-pa-md">
    <div class="q-mb-md">
      <div class="text-h6">
        Drag 'n Drop implementation inside q-markup-table
      </div>
      <div>
        Based on
        <a target="_blank" href="https://drag-and-drop.formkit.com/"
          >https://drag-and-drop.formkit.com/</a
        >
      </div>
    </div>
    <q-markup-table>
      <thead>
        <q-tr>
          <q-th class="text-left" auto-width>Sort order</q-th>
          <template v-for="(col, index) in columns" :key="index">
            <q-th class="text-left">{{ col.label }}</q-th>
          </template>
        </q-tr>
      </thead>
      <tbody ref="dragDropBody">
        <template v-for="element in dragDropItems" :key="element">
          <q-tr :draggable="true">
            <q-td auto-width class="dragDrop__handleContainer">
              <q-icon
                class="dragDrop__handle q-py-sm q-px-md"
                name="fa fa-grip-vertical"
                size="1rem"
              /><!-- alternative name could be drag_handle-->
            </q-td>
            <q-td class="editOnHover">
              {{ element.name }}
              <q-icon
                name="fa fa-pencil"
                class="hoverIcon q-ml-xs q-mb-xs"
                v-if="editable === true"
              />
              <q-popup-edit
                :model-value="element.name"
                title="Name"
                buttons
                @save="
                  (newVal, oldVal) => updateCol(newVal, oldVal, element, 'name')
                "
                v-slot="scope"
                v-if="editable === true"
              >
                <q-input v-model="scope.value" dense autofocus type="text" />
              </q-popup-edit>
            </q-td>
            <q-td>
              {{ element.description }}
              <q-btn
                flat
                class="q-ml-xs"
                size="xs"
                color="black"
                @click="expandMarkupTableRow(element)"
                :icon="
                  element.rowExpand ? 'fa fa-chevron-up' : 'fa fa-chevron-down'
                "
                v-if="editable === true"
              />
            </q-td>
          </q-tr>
          <q-tr :draggable="false" class="no-drag" v-show="element.rowExpand">
            <q-td
              colspan="100%"
              style="border-bottom: 1px solid rgba(0, 0, 0, 0.12)"
              >Example non draggable row
            </q-td>
          </q-tr>
        </template>
      </tbody>
      <tfoot v-if="editable === true">
        <q-tr>
          <q-td colspan="100%" class="text-right">
            <q-btn label="Add row" color="primary" icon="add" @click="addRow" />
          </q-td>
        </q-tr>
      </tfoot>
    </q-markup-table>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { useQuasar } from 'quasar';
import { useDragAndDrop } from '@formkit/drag-and-drop/vue';
import { animations } from '@formkit/drag-and-drop';

const quasar = useQuasar();
const editable = ref(true);

const columns = ref([
  { name: 'name', sortable: true, field: 'name', align: 'left', label: 'Name' },
  { name: 'desc', field: 'desc', align: 'left', label: 'Description' },
]);

const data = ref([
  { id: 1, sortOrder: 0, name: 'Name 1', description: 'Description name 1' },
  { id: 2, sortOrder: 1, name: 'Name 2', description: 'Description name 2' },
  { id: 3, sortOrder: 2, name: 'Name 3', description: 'Description name 3' },
  { id: 4, sortOrder: 4, name: 'Name 4', description: 'Description name 4' },
]); // initial db value could be 2147483647 (Integer.MAX_VALUE)

const [dragDropBody, dragDropItems] = useDragAndDrop(data.value, {
  dragHandle: '.dragDrop__handle',
  draggable: (el) => {
    return !el.classList.contains('no-drag');
  },
  plugins: [animations()],
  handleNodeDrop: (_data) => {
    let dataToEmit = [];
    _data.targetData.parent.data.enabledNodes.forEach((node) => {
      const nodeInOriginalData = [...data.value].find(
        (originalDataItem) => originalDataItem.id === node.data.value.id
      );
      dataToEmit.push({
        [nodeInOriginalData.id]: node.data.index,
      });
    });
    // update sort order on the server here
    console.log({ dataToEmit });
    quasar.notify({
      type: 'positive',
      message:
        'Check the console for drag and drop callback (use dataToEmit to update sort order on the server)',
    });
  },
});

const updateCol = (newVal, oldVal, dto, colName) => {
  quasar.notify({
    type: 'positive',
    message: 'You can update the column to the server',
  });
};
const addRow = () => {
  quasar.notify({
    type: 'positive',
    message: 'Add a new row',
  });
};

const expandMarkupTableRow = (el, reOpen = false) => {
  data.value.forEach((item) => {
    if (item.id !== el.id) {
      item.rowExpand = false;
    } else {
      item.rowExpand = reOpen === true ? true : !el.rowExpand;
    }
  });
};
</script>

<style scoped lang="sass">
.dragDrop__handleContainer
  text-align: center
.dragDrop__handle
  cursor: grab
  &:active
    cursor: grabbing

.editOnHover
  cursor: pointer
  .hoverIcon
    visibility: hidden
  &:hover, &:focus
    .hoverIcon
      visibility: visible
</style>
