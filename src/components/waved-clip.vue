<script lang="ts">
import {
  computed,
  defineComponent,
  onMounted,
  Ref,
  ref,
  toRefs,
  watch
} from '@vue/composition-api';

export default defineComponent({
  name: 'WavedClip',
  props: {
    id: {
      type: String || undefined,
      default: undefined
    },
    imageHeight: {
      default: 'calc(6vh + 22vw)',
      type: String
    },
    percentage: {
      default: 0.2,
      type: Number
    },
    position: {
      type: String,
      default: 'both',
      validator: (value: string) =>
        ['top', 'bottom', 'both'].indexOf(value) !== -1
    }
  },
  setup(props) {
    const { percentage, imageHeight, position, id } = toRefs(props);
    const waveHeightPercentage = ref(0.2);
    const containerRef = ref() as Ref<HTMLElement>;

    let uniqueId = ref('');
    if (id === undefined) {
      uniqueId = ref(
        '_' +
          Math.random()
            .toString(36)
            .substr(2, 9)
      );
    } else {
      uniqueId = ref(id);
    }

    onMounted(() => {
      const content = ref(containerRef.value.children[1]) as Ref<HTMLElement>;
      const image = ref(content.value);

      containerRef.value.style.height = imageHeight.value;
      image.value.style.clipPath = 'url(#' + uniqueId.value + ')';

      //////////////////////////////////////////////////////////////////////////////////////////////////////
      image.value.classList.add('clip-image'); //        <---- That is the line that reproduce the error

      //////////////////////////////////////////////////////////////////////////////////////////////////////
      // image.value.style.objectFit = ' cover'; //       <---- This is how you can 'fix' it directly adding the attributes
      // image.value.style.width = ' 100%';
      // image.value.style.height = ' 100%';

      //////////////////////////////////////////////////////////////////////////////////////////////////////
      // setTimeout(() => {
      //   image.value.classList.add('clip-image');
      // }, 0);
      //^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^This is how you can 'fix' it adding the set

      console.log(image.value.classList);
      //          ^^^^^^^^^^^^^^^^^^^^^ As you will see the classes are present in the log but if you check in the element section from dev tools
      //                                the 'clip-image' class is not present
    });

    watch(
      () => percentage.value,
      percentageVal => {
        if (percentageVal >= 0) {
          if (percentageVal <= 1) {
            waveHeightPercentage.value = percentageVal;
          } else if (percentageVal <= 100) {
            waveHeightPercentage.value = percentageVal / 100;
          }
        }
      },
      { immediate: true }
    );

    const A = computed(() => '0 ' + waveHeightPercentage.value.toString());
    const B = computed(
      () =>
        '0.25 ' +
        (waveHeightPercentage.value - waveHeightPercentage.value * 2).toString()
    );
    const C = computed(() => '0.5 ' + waveHeightPercentage.value.toString());
    const D = computed(
      () =>
        '0.75 ' +
        (waveHeightPercentage.value + waveHeightPercentage.value * 2).toString()
    );
    const E = computed(() => '1 ' + waveHeightPercentage.value.toString());
    const F = computed(
      () => '1 ' + (1 - waveHeightPercentage.value).toString()
    );
    const G = computed(
      () =>
        '0.75 ' +
        (
          1 -
          waveHeightPercentage.value +
          waveHeightPercentage.value * 2
        ).toString()
    );
    const H = computed(
      () => '0.5 ' + (1 - waveHeightPercentage.value).toString()
    );
    const I = computed(
      () =>
        '0.25 ' +
        (
          1 -
          waveHeightPercentage.value -
          waveHeightPercentage.value * 2
        ).toString()
    );
    const J = computed(
      () => '0 ' + (1 - waveHeightPercentage.value).toString()
    );

    const bottomPath = computed(
      () =>
        'M ' +
        F.value +
        ' Q ' +
        G.value +
        ' ' +
        H.value +
        ' , ' +
        I.value +
        ' ' +
        J.value +
        ' V 0 H 1 Z'
    );
    const topPath = computed(
      () =>
        'M ' +
        A.value +
        ' Q ' +
        B.value +
        ' ' +
        C.value +
        ' , ' +
        D.value +
        ' ' +
        E.value +
        ' V 1 H 0 Z'
    );
    const bothPath = computed(
      () =>
        'M ' +
        A.value +
        ' Q ' +
        B.value +
        ' ' +
        C.value +
        ' , ' +
        D.value +
        ' ' +
        E.value +
        ' L ' +
        F.value +
        ' Q ' +
        G.value +
        ' ' +
        H.value +
        ' , ' +
        I.value +
        ' ' +
        J.value +
        ' Z'
    );

    const pathToFollow = computed(() => {
      if (position.value === 'top') {
        return topPath.value;
      } else if (position.value === 'bottom') {
        return bottomPath.value;
      } else {
        return bothPath.value;
      }
    });
    return { containerRef, pathToFollow, uniqueId };
  }
});
</script>

<template>
  <div ref="containerRef" class="waved-image-clip container">
    <svg width="0" height="0">
      <defs>
        <clipPath clipPathUnits="objectBoundingBox" :id="uniqueId">
          <path :d="pathToFollow" />
        </clipPath>
      </defs>
    </svg>
    <slot />
    <div class="clip-image" v-if="false" />
  </div>
</template>

<style lang="scss" scoped>
.container {
  overflow: hidden;
  position: relative;
  width: 100%;
}

.clip-image {
  height: 100%;
  object-fit: cover;
  width: 100%;
}
</style>
