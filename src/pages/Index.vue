<script lang="ts">
import { defineComponent, ref, watch } from '@vue/composition-api';
import { scroll } from 'quasar';
const { getScrollTarget, setScrollPosition } = scroll;

function scrollToSection() {
  setScrollPosition(window, 1000, 250);
}

export default defineComponent({
  name: 'PageIndex',
  setup() {
    const buttonOver = ref(false);
    const listOver = ref(false);

    const isOpen = ref(false);

    function checkOpen() {
      setTimeout(() => {
        isOpen.value = buttonOver.value || listOver.value;
      }, 0);
    }

    watch(
      () => buttonOver.value,
      () => {
        checkOpen();
      }
    );
    watch(
      () => listOver.value,
      () => {
        checkOpen();
      }
    );

    return { isOpen, buttonOver, listOver, scrollToSection };
  }
});
</script>

<template>
  <q-page class="">
    <div class="filler bg-black relative-position">
      <div class="absolute-center">
        <p class="text-white text-weight-bolder">
          Click it and do not click on the page!
        </p>
        <div class="flex justify-center">
          <span class="column">
            <q-icon name="arrow_downward" color="red" size="xl" />
            <q-btn
              icon="arrow_downward"
              round
              outline
              text-color="white"
              @click="scrollToSection()"
            />
          </span>
        </div>
      </div>
    </div>
    <div class="filler" />
    <div class="filler bg-black" />

    <div class="filler relative-position">
      <div class="absolute-center">
        <p class="text-black text-weight-bolder">
          Now just hover this dropdown and leave it!
        </p>
        <div class="flex justify-center">
          <span class="column">
            <q-icon
              name="arrow_downward"
              color="red"
              size="xl"
              class="q-mx-auto"
            />
            <q-btn-dropdown
              v-model="isOpen"
              auto-close
              unelevated
              class="bg-grey"
              content-class="no-shadow bg-primary q-btn-dropdown-content-square"
              @mouseenter.native="buttonOver = true"
              @mouseleave.native="buttonOver = false"
              outline
            >
              <template v-slot:label>
                <div>
                  Dropdown button
                </div>
              </template>
              <q-list
                @mouseenter.native="listOver = true"
                @mouseleave.native="listOver = false"
                bordered
              >
                <q-item v-close-popup class="bg-white" clickable>
                  Option
                </q-item>
                <q-item v-close-popup class="bg-white" clickable>
                  Option
                </q-item>
                <q-item v-close-popup class="bg-white" clickable>
                  Option
                </q-item>
              </q-list>
            </q-btn-dropdown>
          </span>
        </div>
      </div>
    </div>
    <div class="filler bg-black" />
    <div class="filler" />
  </q-page>
</template>

<style lang="scss" scoped>
.filler {
  width: 100%;
  height: 250px;
}

p {
  font-size: 30px;
}
</style>
