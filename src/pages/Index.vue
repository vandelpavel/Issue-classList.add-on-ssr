<template>
  <q-page class="">
    <p>With a class</p>
    <bottom-waved-image waveId="home-bottom-section">
      <template v-slot:image>
        <img src="../assets/ananas.jpg" class="any-class" />
      </template>
    </bottom-waved-image>

    <p>Without a class</p>
    <bottom-waved-image waveId="home-bottom-section">
      <template v-slot:image>
        <img src="../assets/ananas.jpg" />
      </template>
    </bottom-waved-image>
  </q-page>
</template>

<style lang="scss" scoped></style>

<script lang="ts">
import { defineComponent } from '@vue/composition-api';
import BottomWavedImage from 'components/bottom-waved-image.vue';

export default defineComponent({
  name: 'PageIndex',
  components: { BottomWavedImage }
});
</script>
