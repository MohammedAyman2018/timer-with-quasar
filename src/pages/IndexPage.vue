<template>
  <q-pull-to-refresh @refresh="refresh">
    <q-page>
      <section class="q-ma-lg">
        <h2>New Event to track</h2>
        <q-input filled v-model="title" label="Task label" />
        <date-time-input :updateKey="updateKey" @dateChanged="dateChanged" />
        <q-btn
          color="primary"
          @click="addToLocalStorage"
          :disable="!title || !date"
          label="Add"
        />
      </section>
      <div class="q-pa-lg">
        <q-list :key="listKey" bordered separator>
          <q-item v-for="(log, idx) in logs" :key="log.id" clickable v-ripple>
            <q-item-section>
              <q-item-label>{{ log.title }}</q-item-label>
              <q-item-label caption>{{
                formatDistance(new Date(), new Date(log.date), {
                  includeSeconds: true,
                })
              }}</q-item-label>
            </q-item-section>
            <q-item-section side top>
              <q-icon
                name="close"
                color="red"
                @click="removeFromLocalStorage(idx)"
              />
            </q-item-section>
          </q-item>
        </q-list>
      </div>
    </q-page>
  </q-pull-to-refresh>
</template>

<script lang="ts">
import { IEvent } from 'src/components/models';
import { defineComponent, onMounted, Ref, ref } from 'vue';
import DateTimeInput from 'src/components/DateTimeInput.vue';
import { format, formatDistance } from 'date-fns';
import { useQuasar } from 'quasar';

export default defineComponent({
  name: 'IndexPage',
  setup() {
    // Update list every minute
    const listKey = ref(0);
    setInterval(() => {
      listKey.value = Math.random();
    }, 60000);
    let logs: Ref<IEvent[]> = ref([]);
    const getItems = () => {
      const items: string | null = localStorage.getItem('myTimer_Events');
      if (items) {
        logs.value = JSON.parse(items);
      }
    };
    const title = ref('');
    // update date component`
    const updateKey = ref(0);
    const date = ref(format(new Date(), 'yyyy-MM-dd HH:mm'));
    const dateChanged = (val: string) => {
      date.value = val;
    };
    const removeFromLocalStorage = (idx: number) => {
      const items: string | null = localStorage.getItem('myTimer_Events');
      const parsedItems: IEvent[] = items ? JSON.parse(items) : [];
      parsedItems.splice(idx, 1);
      localStorage.setItem('myTimer_Events', JSON.stringify(parsedItems));
      logs.value.splice(idx, 1);
    };
    const addToLocalStorage = () => {
      const items: string | null = localStorage.getItem('myTimer_Events');
      const parsedItems: IEvent[] = items ? JSON.parse(items) : [];
      const newLog: IEvent = {
        id: Number(parsedItems?.length),
        title: title.value,
        date: date.value,
      };
      parsedItems.push(newLog);
      localStorage.setItem('myTimer_Events', JSON.stringify(parsedItems));
      logs.value.push(newLog);
      title.value = '';
      updateKey.value = Math.random();
    };

    onMounted(() => {
      // Make Dark mode
      const $q = useQuasar();
      $q.dark.toggle();
      getItems();
    });
    // eslint-disable-next-line @typescript-eslint/ban-types
    const refresh = (done: Function) => {
      getItems();
      done();
    };
    return {
      logs,
      date,
      title,
      updateKey,
      listKey,
      refresh,
      removeFromLocalStorage,
      dateChanged,
      addToLocalStorage,
      formatDistance,
    };
  },
  components: { DateTimeInput },
});
</script>
