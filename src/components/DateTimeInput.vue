<template>
  <div class="q-py-md" style="max-width: 300px">
    <q-input v-model="date" filled>
      <template v-slot:prepend>
        <q-icon name="event" class="cursor-pointer">
          <q-popup-proxy cover transition-show="scale" transition-hide="scale">
            <q-date v-model="date" mask="YYYY-MM-DD HH:mm">
              <div class="row items-center justify-end">
                <q-btn v-close-popup label="Close" color="primary" flat />
              </div>
            </q-date>
          </q-popup-proxy>
        </q-icon>
      </template>

      <template v-slot:append>
        <q-icon name="access_time" class="cursor-pointer">
          <q-popup-proxy cover transition-show="scale" transition-hide="scale">
            <q-time v-model="date" mask="YYYY-MM-DD HH:mm" format24h>
              <div class="row items-center justify-end">
                <q-btn v-close-popup label="Close" color="primary" flat />
              </div>
            </q-time>
          </q-popup-proxy>
        </q-icon>
      </template>
    </q-input>
  </div>
</template>

<script lang="ts">
import { format } from 'date-fns';
import { defineComponent, watch } from 'vue';
import { ref } from 'vue-demi';

export default defineComponent({
  props: {
    updateKey: {
      type: Number,
      default: () => 0,
    },
  },
  emits: ['dateChanged'],
  setup(props, { emit }) {
    const date = ref(format(new Date(), 'yyyy-MM-dd HH:mm'));
    watch(date, (val) => {
      emit('dateChanged', val);
    });
    watch(
      () => props.updateKey,
      () => {
        console.log('dspodsupaj');
        date.value = format(new Date(), 'yyyy-MM-dd HH:mm');
      }
    );
    return {
      date,
    };
  },
});
</script>
