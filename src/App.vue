<template>
  <div class="px-4 mx-auto max-w-7xl sm:px-6 lg:px-8 bg-gray-800">
    <div
      class="text-4xl font-bold tracking-wide p-20 text-center text-gray-200"
    >
      EOS Super Friends!
    </div>
    <div
      class="text-4xl font-bold tracking-wide p-20 text-center text-gray-200"
    >
      Worth: {{ formattedValue }}
    </div>
    <div
      v-if="profit"
      :class="`text-4xl font-bold tracking-wide p-20 text-center text-gray-200 ${
        inProfit ? 'text-green-500' : 'text-red-500'
      }`"
    >
      Profit: {{ profit }}
    </div>
    <div
      class="
        max-w-7xl
        mx-auto
        my-6
        flex flex-col
        justify-center
        md:flex-row
        flex-wrap
        basis-0
        shrink
        grow
        gap-4
      "
    >
      <div
        v-for="member in members"
        :key="member.name"
        class="
          rounded-lg
          bg-gray-500
          w-4/6
          p-10
          max-w-sm
          flex flex-col
          justify-center
          m-auto
        "
      >
        <div class="max-w-lg flex flex-col justify-center basis-0 shrink grow">
          <img
            class="rounded-lg w-40 h-40 mx-auto"
            :src="member.picture"
            :alt="member.name"
          >
          <h1 class="font-semibold text-gray-200 text-center mt-4 text-2xl">
            {{ member.name }}
          </h1>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import axios from 'axios'
import {
  combineLatest,
  map,
  of,
  shareReplay,
  startWith,
  switchMap,
  timer,
} from 'rxjs'
import { useObservable } from '@vueuse/rxjs'

var formatter = new Intl.NumberFormat('en-US', {
  style: 'currency',
  currency: 'USD',
})

export default defineComponent({
  components: {},
  setup() {
    const members: { name: string; picture: string }[] = [
      {
        name: 'Aaron Cox',
        picture: 'Qmb7yzsVfEssigjHbcX46g4RPPgvEzjqiAxa1nmkm76APo',
      },
      {
        name: 'Brandon Lovejoy',
        picture: 'QmVpVRdr5U7XuWprRUhr4tDEjitxV1b7mf4Ui78ba81fkV',
      },
      {
        name: 'Chris Barnes',
        picture: 'QmWyawMvUQMf7FmaxpJyot5efmVFWC9gF8JVUcR9ovicwX',
      },
      {
        name: 'Randall Roland',
        picture: 'QmaXBFy6Xh3K7NM5BHJhzYXgTmbVzCgdpn7YeBiqhAsCQv',
      },
      {
        name: 'Jesse Jaffe',
        picture: 'QmSx7cT8WoPR9tyDJh5D4snjZcMYqiiXCCnKWYMJJpLs2Q',
      },
      {
        name: 'John Williamson',
        picture: 'QmWU7jyrFxQF5LfMrKyCdb4b1jt4fzDsk3w2KHJ2mvjn5F',
      },
    ].map((x) => ({
      ...x,
      picture: `https://ipfs.orelo.software/ipfs/${x.picture}`,
    }))

    const fetchUsdAudPrice = async () => {
      const { data } = await axios.get<{ data: { AUD: number } }>(
        'https://freecurrencyapi.net/api/v2/latest?base_currency=USD&apikey=0eba3020-7e39-11ec-aef6-1f97d7d5f210',
      )
      return data.data.AUD
    }

    const fetchBinancePrice = async () => {
      console.count('binance')
      const { data } = await axios.get<{ symbol: string; price: string }>(
        'https://api.binance.com/api/v3/ticker/price?symbol=BTCUSDT',
      )
      return Number(data.price)
    }

    const btcHolding = 0.13640358

    const seconds$ = timer(0, 7000)
    const minute$ = timer(0, 60000)

    const usdAud$ = minute$.pipe(switchMap(() => fetchUsdAudPrice()))

    const usdBtc$ = seconds$.pipe(
      switchMap(() => fetchBinancePrice()),
      shareReplay(1),
    )

    const audValue$ = combineLatest([usdAud$, usdBtc$, of(btcHolding)]).pipe(
      map(([usdAud, usdBtc, btcHolding]) => {
        const usdWorth = usdBtc * btcHolding
        return usdAud * usdWorth
      }),
    )

    const profit$ = audValue$.pipe(map((value) => value - 7000))

    const profit = useObservable(profit$.pipe(map(formatter.format)))

    const formattedValue = useObservable(audValue$.pipe(map(formatter.format)))

    const inProfit = useObservable(
      profit$.pipe(
        map((price) => price > 0),
        startWith(false),
      ),
    )

    return { members, profit, formattedValue, inProfit }
  },
})
</script>

<style>
body {
  background: "black";
}
</style>
