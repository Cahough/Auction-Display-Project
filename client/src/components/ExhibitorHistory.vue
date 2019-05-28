<template>
    <main class="exhibitorHistory">
      <h1 class="history_display_header">Exhibitor History</h1>
      <table class ="exhibitor_history_table">
        <thead>
          <tr class="table_headers">
            <th>Exhibitor Name</th>
            <th>Buyers</th>
            <th>Addons</th>
          </tr>
        </thead>
        <tbody>
          <tr class="exhibitor_table_rows">
            <td>{{exhibitor.fullName}}</td>

            <!-- Buyers -->
            <td>
              <table>
                <tbody>
                  <tr v-if="buyer !== null" v-for="buyer in buyers" :key="buyer._id">
                    <td>{{ buyer.name }}</td>
                  </tr>
                </tbody>
              </table>
            </td>

            <!-- Addons / Addon purchase -->
            <td>
              <table>
                <tbody>
                <section v-if="(transaction.purchaseType === addon) && transactions.length" v-for="transaction in transactions" :key="transaction._id">
                  <tr v-if="addOn.bidderNumber == transaction.bidderNumber" v-for="addOn in addOns" :key="addOn._id">
                    <td class="addonRow">{{ addOn.name }}</td>
                    <td class="addonRow">${{ transaction.purchaseAmount }}</td>
                  </tr>
                </section>
                </tbody>
              </table>
            </td>

          </tr>
        </tbody>
      </table>
    </main>
</template>

<script>
  import {mapState, mapActions} from 'vuex'
  export default {
    data() {
      return {
        saleNumber: 0,
        exhibitor: [],
        previousExhibitor: [],
        buyers: [],
        addOns: [],
        transactions: [],
        previousSaleNumber: 0,
        displayID: 0,
        bidderNumbers: 0,
        showCurrentSale: false,
        showPreviousSale: false,
        buyer: "Buyer",
        addon: "Addon"
      }
    },
    computed: {
      ...mapState({
        // saleNumber: state => state.saleNumber,
        // transactions: state => state.transactions
      })
    },
    created: function() {
      setInterval(() => this.fetchSaleNumber(), 1000)
      this.fetchSaleNumber()
    },
    methods: {
      async fetchSaleNumber() {
        // retrieve data for comparison
        let uri = `http://${process.env.HOST_NAME}:8081/display`
        await this.axios.get(uri).then(response => {
          if (response.data.length >= 1) {
            this.displayID = response.data[0]._id
            this.saleNumber = response.data[0].saleNumber
            this.showCurrentSale = response.data[0].showCurrentSale
            this.showPreviousSale = response.data[0].showPreviousSale
          }
        })
        // checks flag, retrieves new sale number, then reloads pages in the display functions
        if (this.showCurrentSale === true) {
          this.displayCurrentSale()
        }
        if (this.showPreviousSale === true) {
          this.displayPreviousSale()
        }
        this.fetchData()
      },
      async fetchData() {
        // fetches all data
        this.fetchExhibitor()
        this.fetchPreviousExhibitor()
        this.fetchBuyers()
        this.fetchTransactions()
        this.fetchAddOns()
        // this.fetchAddOnBuyers()
      },
      async displayCurrentSale() {
        let uri = `http://${process.env.HOST_NAME}:8081/display/${this.displayID}`
        this.showCurrentSale = false
        let state = {
          saleNumber: this.saleNumber,
          previousSaleNumber: this.previousSaleNumber,
          showCurrentSale: this.showCurrentSale,
          showPreviousSale: this.showPreviousSale
        }
        await this.axios.put(uri, state).then(response => {})
      },
      async displayPreviousSale() {
        let uri = `http://${process.env.HOST_NAME}:8081/display/${this.displayID}`
        this.showPreviousSale = false
        let updatedCheck = {
          saleNumber: this.saleNumber,
          previousSaleNumber: this.previousSaleNumber,
          showCurrentSale: this.showCurrentSale,
          showPreviousSale: this.showPreviousSale
        }
        await this.axios.put(uri, updatedCheck).then(response => { })
        window.location.reload()
      },
      async fetchExhibitor() {
        // fetches the current exhibitor by its sale number
        let url = `http://${process.env.HOST_NAME}:8081/exhibitor/saleNumber/${this.saleNumber}`
        await this.axios.get(url).then(response => {
          this.exhibitor = response.data
        })
      },
      async fetchPreviousExhibitor() {
        // gets the previous sale number from the most recent transaction
        await this.axios.get(`http://${process.env.HOST_NAME}:8081/transaction`).then(response => {
          let index = response.data.length - 1
          while (response.data.length > 0) {
            if (response.data[index].purchaseType === "Buyer") {
              this.previousSaleNumber = response.data[index].saleNumber
              break
            } else index--
          }
        })
        // fetches the previous exhibitor by the previous sale number
        let url = `http://${process.env.HOST_NAME}:8081/exhibitor/saleNumber/${this.previousSaleNumber}`
        await this.axios.get(url).then(response => {
          this.previousExhibitor = response.data
        })
      },
      async fetchTransactions() {
        let url = `http://${process.env.HOST_NAME}:8081/transaction/saleNumber/${this.previousSaleNumber}`
        await this.axios.get(url).then(response => {
          this.transactions = response.data
        })
      },
      async fetchBuyers() {
        this.parseBidderNumber()
        for (let i = 0; i < this.bidderNumbers.length; i++) {
          let uri = `http://${process.env.HOST_NAME}:8081/buyer/bidderNumber/${this.bidderNumbers[i]}`
          await this.axios.get(uri).then(response => {
            this.buyers[i] = response.data
          })
        }
      },
      async parseBidderNumber() {
        this.bidderNumbers = this.transactions[0].bidderNumber.split('-')
      },
      async fetchAddOns() {
        let uri = `http://${process.env.HOST_NAME}:8081/buyer`
        await this.axios.get(uri).then(response => {
          this.addOns = response.data
        })
      },
      ...mapActions(['setSaleNumber', 'setTransactions'])
    }
  }
</script>
<css>
.history_display_header {
  font-size: 50px;
  line-height: 100%;
  color: #339966;
  text-align: center;
  text-transform: uppercase; }

.exhibitor_history_table {
  text-align: left;
  margin: 50px 10px;
  padding: 10px 15px;
  border: 1px solid #339966;
  color: #404040;
  width: 99%; }

.table_headers {
  width: 30%;
  color: #339966;
  font-size: 25px; }

.exhibitor_table_rows {
  font-size: 25px;
  margin: 10px 10px;
  padding: 5px 10px; }

.addonRow {
  width: 50% }


</css>
