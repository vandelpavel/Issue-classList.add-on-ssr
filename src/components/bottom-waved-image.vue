<script lang="ts">
import {
  computed,
  defineComponent,
  onMounted,
  Ref,
  ref,
  toRefs
} from '@vue/composition-api';
import WavedClip from 'components/waved-clip.vue';
import { Screen } from 'quasar';

export default defineComponent({
  name: 'BottomWavedElement',
  components: { WavedClip },
  props: {
    waveId: {
      type: String || undefined,
      default: undefined
    },
    imageHeight: {
      default: '700px',
      type: String
    }
  },
  setup(props) {
    const hostRef = ref() as Ref<HTMLElement>;

    const { imageHeight, waveId } = toRefs(props);
    const underWaveRef = ref() as Ref<HTMLElement>;

    const percentage = computed(() => {
      if (Screen.name === 'sm') {
        return 0.06;
      } else if (Screen.name === 'xs') {
        return 0.04;
      } else return 0.1;
    });

    onMounted(() => {
      underWaveRef.value.style.height = imageHeight.value;
      underWaveRef.value.style.clipPath = `url(#bottom-waved-${waveId.value})`;
      hostRef.value.style.height = 'calc(' + imageHeight.value + ' + 100px)';
    });

    return {
      hostRef,
      percentage,
      underWaveRef
    };
  }
});
</script>

<template>
  <div class="container" ref="hostRef">
    <div ref="underWaveRef" class="under-wave" />
    <div class="bottom-waved-element relative-position">
      <waved-clip
        :id="'bottom-waved-' + waveId"
        :percentage="percentage"
        position="top"
        :imageHeight="imageHeight"
      >
        <slot name="image" />
      </waved-clip>
    </div>
  </div>
</template>

<style lang="scss" scoped>
$button-font-size: 14px;
$button-letter-spacing: 1px;
$under-wave-background-color: #eff4ea;
$under-wave-height: 500px;

.container {
  position: relative;
}

.bottom-waved-element {
  bottom: 0;
  position: absolute;
  width: 100%;
}

.under-wave {
  background-color: $under-wave-background-color;
  height: $under-wave-height;
  position: absolute;
  top: 0;
  width: 100%;
}
</style>
