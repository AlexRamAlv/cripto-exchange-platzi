<template>
  <table>
    <thead>
      <tr class="bg-gray-100 border-b-2 border-gray-400">
        <th></th>
        <th :class="{up: this.sortOrder === 1, down: this.sortOrder === -1}">
          <span class="underline cursor-pointer" @click="changeSortOrder">Ranking</span>
        </th>
        <th>Nombre</th>
        <th>Precio</th>
        <th>Cap. de Mercado</th>
        <th>Variación 24hs</th>
        <td class="hidden sm:block">
          <input
            class="bg-gray-100 focus:outline-none border-b border-gray-400 py-2 px-4 block w-full appearance-none leading-normal"
            id="filter"
            placeholder="Buscar..."
            type="text"
            v-model="filter"
          />
        </td>
      </tr>
    </thead>
    <tbody>
      <tr class="border-b border-gray-200 hover:bg-blue-100" v-for="a in filteredAssets" :key="a.id">
        <td>
          <img class="w-6 h-6" :src="`https://static.coincap.io/assets/icons/${a.symbol.toLowerCase()}@2x.png`" :alt="a.name">
        </td>
        <td>{{ a.rank }}</td>
        <td>
          <router-link :to="{name: 'coin-detail', params:{id: a.id}}" class="text-blue-500 font-bold hover:underline">
          {{ a.name }}
          </router-link>
          <small class="ml-1 text-gray-500">{{ a.symbol }}</small></td>
        <td>{{ a.priceUsd | dollar }}</td>
        <td>{{ a.marketCapUsd | dollar }}</td>
        <!-- also using class -->
        <!-- :class="a.changePercent24Hr.includes("-")? text-red-600 : text-green-600" -->
        <td :style="{color: a.changePercent24Hr < 0 ?'red': 'green'}">{{ a.changePercent24Hr | percentege}}</td>
        <td class="hidden sm:block">
          <px-button @custom-click="goToCoin(a.id)">
            Detalle
          </px-button>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script>
import PxButton from "@/components/PxButton";
export default {
  name: "PxTable",
  components:{PxButton},
  props: {
    assetsTable: {
      type: Array,
      default: () => []
    }
  },
  data(){
    return{
      filter:"",
      sortOrder: 1
    }
  },
  computed:{
    filteredAssets(){

      const altOrder = this.sortOrder === 1 ? -1 : 1

      return this.assetsTable.filter(
        a => 
        a.symbol.toLowerCase().includes(this.filter.toLowerCase()) ||
        a.name.toLowerCase().includes(this.filter.toLowerCase())
      )
      .sort((a, b) => {
        if(parseInt(a.rank) > parseInt(b.rank)){ return this.sortOrder}
        return altOrder
      })
    }
  },

  methods:{
    goToCoin(id){
      this.$router.push({name: 'coin-detail', params:{id: id}})
    },

    changeSortOrder(){
      this.sortOrder = this.sortOrder === 1 ? -1 : 1
    }
  }
};
</script>

<style scoped>
.up::before {
  content: "👆";
}

.down::before {
  content: "👇";
}

td {
  padding: 20px 0px;
  font-size: 0.6rem;
  text-align: center;
}

th {
  padding: 5px;
  font-size: 0.6rem;
}

@media (min-width: 640px) {
  td,
  th {
    padding: 20px;
    font-size: 1rem;
  }

  th {
    padding: 12px;
  }
}
</style>