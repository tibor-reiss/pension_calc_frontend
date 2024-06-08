<template>
  <div class="home">
    <section class="hero is-small is-dark mb-6">
        <div class="hero-body has-text-centered">
            <p class="title mb-3">
                Welcome to the PensionCalculator
            </p>
            <p class="subtitle">
                Retire rich
            </p>
        </div>
    </section>

    <div style="margin-left: 20px">
      <div class="userInput" style="margin-bottom: 50px">
        <form @submit.prevent="postGenerateCalc">
          <div style="margin-bottom: 10px">
            <label class="inputLabel">Purchase Price: </label>
            <input type="number" step="1000" v-model="monthly_contribution" placeholder="E.g. 100000" />
          </div>
          <div style="margin-bottom: 10px">
            <label class="inputLabel">Interest Rate in %: </label>
            <input type="number" step="0.01" v-model="yearly_interest_rate" placeholder="E.g. 5.5" />
          </div>
          <div style="margin-bottom: 10px">
            <label class="inputLabel">Down Payment in $: </label>
            <input type="number" step="1000" v-model="starting_capital" placeholder="40000" />
          </div>
          <div style="margin-bottom: 10px">
            <label class="inputLabel">Mortgage Term: </label>
            <input type="text" v-model="term" placeholder="5 years" />
          </div>
          <button type="submit" class="button is-success">Generate Calc</button>
        </form>
      </div>

      <div class="pensionTable">
        <DataTable
          :value="latestCalcs"
          resizableColumns
          showGridlines
          columnResizeMode="expand"
          tableStyle="max-width: 95%"
        >
          <Column field="term" header="Term" sortable style="width: 20%"></Column>
          <Column field="monthly_contribution" header="Monthly Contribution" sortable style="width: 20%"></Column>
          <Column field="yearly_interest_rate" header="Yearly Interest Rate" sortable style="width: 20%"></Column>
          <Column field="total_capital" header="Total Capital" sortable style="width: 20%"></Column>
        </DataTable>
      </div>
    </div>

  </div>
</template>

<script>
import axios from 'axios'
import { toast } from 'bulma-toast'
import DataTable from "primevue/datatable";
import Column from "primevue/column";
import 'primevue/resources/themes/saga-blue/theme.css';

export default {
  name: 'HomeView',
  data() {
    return {
      latestCalcs: [],
    }
  },
  components: {
    DataTable,
    Column,
  },
  mounted() {
    this.getLatestCalcs()
  },
  methods: {
    getLatestCalcs() {
      axios
        .get('/api/v1/latest-calcs/')
        .then(response => {
          this.latestCalcs = response.data
        })
        .catch(error => {
          console.log(error)
        })
    },
    postGenerateCalc() {
      console.log(
        this.monthly_contribution,
        this.yearly_interest_rate,
        this.starting_capital,
        this.term,
      );
      const calc = {
        "monthly_contribution": this.monthly_contribution,
        "yearly_interest_rate": this.yearly_interest_rate,
        "starting_capital": this.starting_capital,
        "term": this.term,
      }
      axios
        .post('/api/v1/generate-calc/', calc)
        .then(() => {
          this.getLatestCalcs()
          toast({
            message: "New calc added",
            type: 'is-success',
            duration: 2000,
            position: 'bottom-right',
          })
        })
        .catch(error => {
          console.log(error)
          toast({
            message: "Wrong inputs",
            type: 'is-danger',
            duration: 3000,
            position: 'center',
          })
        })
    },
  }
}
</script>
