<template>
  <v-card>
    <v-card-title>
      <v-row
        class="pa-4"
        align="start"
        justify="end"
      >
        <v-col
          cols="12"
          md="6"
        >
          Jobs
          <div class='flex-grow-1'></div>
        </v-col>
        <v-text-field
          v-model='search'
          append-icon='search'
          label='Search'
          single-line
          hide-details
          style="max-width: 50%"
          class="ma-5"
        ></v-text-field>
      </v-row>
    </v-card-title>

    <v-row
      class="ma-8 pa-8"
      align="start"
      justify="end"
    >
      <v-autocomplete
        label="Location"
        :items="location"
        filled
        rounded
        style="max-width: 50%"
        v-model="locationSelect"
        clearable
      ></v-autocomplete>

    </v-row>

    <v-data-table
      :loading=fetchData
      loading-text="Loading... Please wait"
      multi-sort
      dark
      :search='search || locationSelect'
      :headers='headers'
      :items='jobList'
      class='elevation-1'
      :items-per-page=pageItems
    >
      <template v-slot:item.source="{ item }">
        <v-chip
          color="pink"
          label
          outlined
          text-color="white"
          :href="item.applylink"
        >
          <v-icon left>mdi-label</v-icon>
          {{ item.source }}
        </v-chip>
      </template>
    </v-data-table>
  </v-card>
</template>

<script>
export default {
  name: 'jobTable',
  data() {
    return {
      search: '',
      headers: [
        {
          text: 'Company',
          align: 'left',
          sortable: false,
          value: 'companyname',
        },
        { text: 'Job Title', value: 'title' },
        { text: 'Location', value: 'location' },
        { text: 'Experience', value: 'experience' },
        { text: 'Skills', value: 'skills' },
        { text: 'Source', value: 'source' },
      ],
      jobList: [],
      fetchData: true,
      pageItems: 25,
      location: [],
      locationSelect: '',
    };
  },
  mounted() {
    // get json file from web
    fetch('https://nut-case.s3.amazonaws.com/jobs.json', { mode: 'cors' })
      .then(jsonData => jsonData.json()
        .then((data) => {
          this.jobList = data.data;
          this.fetchData = false;
          const l = [];
          this.jobList.map(el => l.push(el.location));
          this.location = [...new Set(l)];
        }))
      .catch(e => console.log(e));
  },
};
</script>

<style scoped>
  .theme--dark.v-data-table thead tr th{
    font-size: 14px;
  }
</style>
