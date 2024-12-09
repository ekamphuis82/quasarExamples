<template>
  <q-dialog v-model="visible">
    <q-card>
      <q-card-section class="q-pb-none">
        <div class="text-h6">Drag 'n Drop Dialog</div>
      </q-card-section>
      <q-card-section class="q-pt-none">
        <drag-n-drop :inside-dialog="true" />
      </q-card-section>
      <q-card-actions align="right">
        <q-btn color="primary" label="Close" v-close-popup />
      </q-card-actions>
    </q-card>
  </q-dialog>
</template>

<script setup>
import {ref} from "vue";
import DragNDrop from "pages/DragNDrop.vue";

const visible = ref(false);

const show = () => {
  visible.value = true;
}

defineExpose({
  show
})

</script>


<style scoped lang="sass">

</style>
