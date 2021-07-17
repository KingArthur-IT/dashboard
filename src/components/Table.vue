<template>
  <v-data-table
    dense
    :headers="headers"
    :items="arrCountries"
    item-key="name"
    class="elevation-1"
  >
  <template v-slot:[`item.today_confirmed`]="{ item }">
      <v-chip
        :color="getColor(item.today_confirmed)"
        dark
        class="table-chip"
      >
        {{ item.today_confirmed }}
      </v-chip>
</template>
</v-data-table>
</template>

<script>

export default ({
    props: [
        'arrCountries'
    ],
    data: () => ({
        headers: [
        {
          text: 'name',
          align: 'start',
          sortable: true,
          value: 'name',
        },
        { text: 'Population', value: 'population' },
        { text: 'Total confirmed', value: 'total_confirmed' },
        { text: 'Total recovered', value: 'total_recovered' },
        { text: 'Total deaths', value: 'total_deaths' },
        { text: 'Today confirmed', value: 'today_confirmed' },
        { text: 'Today deaths', value: 'today_deaths' },
      ],
    }),
    methods: {
      getColor (value) {
        if (value > 400) return 'red'
        else if (value > 200) return 'orange'
        else return 'green'
      },
    },
})
</script>

<style scoped>
    .table-chip{
        min-width: 50px;
        margin: 3px;
    }
</style>