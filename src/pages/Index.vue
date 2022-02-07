<template>
  <q-page class="row">
    <div class="col-12 q-pa-md">
      <q-input name="numberOfPlayer" v-model="numberOfPlayer" label="Number of player" />
      <div class="q-pa-md q-gutter-sm">
        <q-btn color="primary" label="Set total player" @click="setPlayer" />
        <q-btn color="primary" label="Distribute cards to all players" @click="distributeCards" />
      </div>
      <h5>Results</h5>
      <q-list>
        <q-item v-for="player in state.players" :key="player.name">
          <div class="q-pa-md text-weight-bold">{{ player.name }}</div>
          <div class="q-pa-md" v-if="player.cards.length > 0">{{ player.cards.join(', ') }}</div>
        </q-item>
      </q-list>
    </div>
  </q-page>
</template>

<script lang="ts">
import { defineComponent, reactive, ref } from 'vue';
import { api } from 'boot/axios'
import { useQuasar } from 'quasar'
import { Player } from 'src/components/models';

export default defineComponent({
  name: 'PageIndex',
  components: {  },
  setup() {

    const $q = useQuasar()
    const data = ref(null)
    const numberOfPlayer = ref('')
    let players: Player[] = []

    let state = reactive({
      players
    });

    function setPlayer () {
      api.post('/api/players/set', {
        n: numberOfPlayer.value
      })
      .then((response) => {
        state.players = (<Player[]>response.data)
        console.log(state.players)
      })
      .catch(() => {
        $q.notify({
          color: 'negative',
          position: 'top',
          message: 'Loading failed',
          icon: 'report_problem'
        })
      })
    }

    function distributeCards () {
      api.post('/api/players/distribute/all')
      .then((response) => {
        // console.log(response.data)
        state.players = (<Player[]>response.data)
      })
      .catch(() => {
        $q.notify({
          color: 'negative',
          position: 'top',
          message: 'Loading failed',
          icon: 'report_problem'
        })
      })
    }

    return {
      numberOfPlayer,
      data,
      setPlayer,
      distributeCards,
      players,
      state
    }
  },
});
</script>
