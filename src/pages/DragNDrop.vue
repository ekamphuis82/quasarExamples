<template>
  <div class="q-pt-md" :class="{ 'q-px-md' : !insideDialog }">
    <div class="q-mb-md dragDropHead" v-if="!insideDialog">
      <div class="text-h6 dragDropHead__header">
        <span>Drag 'n Drop implementation inside q-markup-table</span>
        <q-btn color="primary" label="Inside dialog" @click="openDragDropDialog" outline />
      </div>
      <div>
        Based on
        <a target="_blank" href="https://drag-and-drop.formkit.com/">
          https://drag-and-drop.formkit.com/
        </a>
      </div>
    </div>
    <q-markup-table :class="getTableContainerClassList">
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
          <q-tr :draggable="false" class="no-drag" v-show="element?.rowExpand">
            <q-td
              colspan="100%"
              style="border-bottom: 1px solid rgba(0, 0, 0, 0.12)"
            >Example non draggable row `{{ element.name }}`
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
    <drag-n-drop-dialog ref="dragDropDialog" />
  </div>
</template>

<script setup>
import {computed, ref} from 'vue';
import { useQuasar } from 'quasar';
import { useDragAndDrop } from '@formkit/drag-and-drop/vue';
import { animations, state } from '@formkit/drag-and-drop';
import DragNDropDialog from "components/dialogs/DragNDropDialog.vue";

const quasar = useQuasar();

const props = defineProps({
  insideDialog: {
    type: Boolean,
    default: false
  }
})

const dragDropDialog = ref()

const editable = ref(true);
const isDragging = ref(false);
const tableStickyZIndex = computed(() => isDragging.value ? '0' : '2');
const getTableContainerClassList = computed(() => {
  let classList = 'tableContainer'
  if (isDragging.value) classList += ' -isDragging'
  if (props.insideDialog) classList += ' -isInsideDialog'
  return classList
})

const columns = ref([
  { name: 'name', sortable: true, field: 'name', align: 'left', label: 'Name' },
  { name: 'desc', field: 'desc', align: 'left', label: 'Description' },
]);

const data = ref([
  { id: 1, sortOrder: 0, name: 'Name 1', description: 'Description name 1' },
  { id: 2, sortOrder: 1, name: 'Name 2', description: 'Description name 2' },
  { id: 3, sortOrder: 2, name: 'Name 3', description: 'Description name 3' },
  { id: 4, sortOrder: 4, name: 'Name 4', description: 'Description name 4' },
  { id: 5, sortOrder: 5, name: 'Name 5', description: 'Description name 6' },
  { id: 6, sortOrder: 6, name: 'Name 6', description: 'Description name 6' },
  { id: 7, sortOrder: 7, name: 'Name 7', description: 'Description name 7' },
  { id: 8, sortOrder: 8, name: 'Name 8', description: 'Description name 8' },
  { id: 9, sortOrder: 9, name: 'Name 9', description: 'Description name 9' },
  { id: 10, sortOrder: 10, name: 'Name 10', description: 'Description name 10' },
  { id: 11, sortOrder: 11, name: 'Name 11', description: 'Description name 11' },
  { id: 12, sortOrder: 12, name: 'Name 12', description: 'Description name 12' },
  { id: 13, sortOrder: 13, name: 'Name 13', description: 'Description name 13' },
  { id: 14, sortOrder: 14, name: 'Name 14', description: 'Description name 14' },
  { id: 15, sortOrder: 15, name: 'Name 15', description: 'Description name 15' },
  { id: 16, sortOrder: 16, name: 'Name 16', description: 'Description name 16' },
  { id: 17, sortOrder: 17, name: 'Name 17', description: 'Description name 17' },
  { id: 18, sortOrder: 18, name: 'Name 18', description: 'Description name 18' },
  { id: 19, sortOrder: 19, name: 'Name 19', description: 'Description name 19' },
  { id: 20, sortOrder: 20, name: 'Name 20', description: 'Description name 11' }

]); // initial db value could be 2147483647 (Integer.MAX_VALUE)

const [dragDropBody, dragDropItems] = useDragAndDrop(data.value, {
  dragHandle: '.dragDrop__handle',
  draggable: (el) => {
    return !el.classList.contains('no-drag');
  },
  plugins: [animations()],
  dragPlaceholderClass: '-dragActive',
  handleNodeDrop: (_data) => {
    isDragging.value = false;
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

state.on("dragStarted", () => {
  isDragging.value = true
})
state.on("dragEnded", () => {
  isDragging.value = false
})

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
      item.rowExpand = reOpen === true ? true : !el?.rowExpand;
    }
  });
};

const openDragDropDialog = () => dragDropDialog.value.show()

</script>

<style scoped lang="sass">
.dragDropHead
  display: flex
  flex-flow: column wrap
  gap: 1rem
.dragDropHead__header
  display: flex
  flex-flow: row wrap
  gap: .5rem 1rem
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
.tableContainer
  //height: 300px
  max-height: calc(100vh - 70px)
  &.-isInsideDialog
    max-height: calc(100vh - 200px)
  &.-isDragging
    &:deep(.q-table tbody td:before)
      background: none // override quasar default rgba(0, 0, 0, 0.03)
    &:deep(.q-table tbody tr.-dragActive td:before)
      background: rgba(0, 0, 0, 0.03)
      position: absolute
      inset: 0
      content: ""
  :deep(tfoot)
    position: sticky
    bottom: 0
    z-index: v-bind('tableStickyZIndex')
    background: #FFF
    tr td
      border-top: 1px solid rgba(0, 0, 0, 0.12)
  :deep(td)
    z-index: 1
  :deep(td), :deep(th)
    background: #FFF
  :deep(.q-table thead)
    tr
      &:first-child
        th
          top: 0
      th
        position: sticky
        z-index: v-bind('tableStickyZIndex')
</style>
