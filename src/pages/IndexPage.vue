<template>
  <q-page class="row justify-evenly">
    <div class="column items-center q-px-md">
      <div class="text-h4 q-mb-md">Quasar examples</div>
      <div class="row q-mt-md q-col-gutter-md">
        <div class="col-12 col-sm-6">
          <router-link class="card-link" :to="{ name: 'dragAndDrop' }">
            <q-card>
              <!-- <img src="https://cdn.quasar.dev/img/components/select.jpg" /> -->
              <img src="~assets/select.jpg" />
              <q-card-section>
                <div class="text-primary text-weight-bold">Drag 'n Drop</div>
              </q-card-section>
              <q-card-section
                class="page-all__card-description text-dark q-pt-none"
              >
                Markup table with drag 'n drop sort option
              </q-card-section>
            </q-card>
          </router-link>
        </div>
      </div>
    </div>
  </q-page>
</template>

<script setup lang="ts">
import { ref } from 'vue';
</script>
