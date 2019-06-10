<template>
    <main class="exhibitorHistory">
      <h1 class="history_display_header">Exhibitor History</h1>
      <table class ="exhibitor_history_table">
        <thead>
          <tr>
            <th class="table_c1">Sale #</th>
            <th class="table_c2">Exhibitor Name</th>
            <th class="table_c3">Buyers</th>
            <th class="table_c4">Addons</th>
          </tr>
        </thead>
        <tbody>
          <tr class="exhibitor_table_rows">
            <td class="sale_number">{{this.saleNumber}}</td>
            <td>{{exhibitor.fullName}}</td>

            <!-- Buyers -->
            <td>
              <table>
                <tbody>
                  <tr class="buyer_rows" v-for="b in buyerNumbers" :key="b">
                    <td>{{ buyers[b-1].name }}</td>
                  </tr>
                </tbody>
              </table>
            </td>

            <!-- Addons / Addon purchase -->
            <td>
              <table>
                <tbody>
                  <tr class="addon_rows" v-if="addon.purchaseType != buyer" v-for="addon in addons" :key="addon._id">
                    <td>{{ addon.name }}</td>
                    <td>{{ addon.purchaseAmount }}</td>
                  </tr>
                </tbody>
              </table>
            </td>

          </tr>

          <tr class ="table_tail">
            <td></td>
            <td></td>
            <td v-if="transactions[0] != null">Purchase Amount: ${{transactions[0].purchaseAmount}}</td>
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
        exhibitorDisplaySpeed: 5000, // milliseconds
        saleNumber: 0,
        maxSaleNumber: 0,
        exhibitor: [],
        buyers: [],
        addons: [],
        transactions: [],
        buyerNumbers: [],
        buyer: "Buyer"
      }
    },
    created: function() {
      setInterval(() => this.fetchSaleNumber(), this.exhibitorDisplaySpeed)
      this.fetchSaleNumber()
    },
    computed: {
      ...mapState({
        // saleNumber: state => state.saleNumber,
        // transactions: state => state.transactions
      }) /* ,
      currentTransactions: function() {
        this.fetchAddons()
        this.logArr()
        console.log("Ctrans reached")
        return this.transactions.filter(addon => addon.saleNumber == this.saleNumber)
      } */
    },
    methods: {
      async fetchSaleNumber() {
        let uri = `http://${process.env.HOST_NAME}:8081/display`
        try {
          await this.axios.get(uri).then(response => {
            if (response.data.length >= 1) {
              if (this.maxSaleNumber < response.data[0].saleNumber) this.maxSaleNumber = response.data[0].saleNumber // Updates max sale number to loop through.
            }
          })
        } catch (err) {
          console.log("Waiting for display entry")
          // console.log(err)
        }
        if (this.saleNumber < this.maxSaleNumber - 1) {
          this.saleNumber++
        } else if (this.maxSaleNumber != 0) {
          this.saleNumber = 1
        }

        if (this.maxSaleNumber > 0) this.fetchData()
      },
      async fetchData() {
        this.fetchTransactions()
        this.fetchExhibitor()
        this.fetchBuyers()
        this.fetchBuyerNumbers()
        this.fetchAddons()
      },
      async fetchBuyerNumbers() {
        this.buyerNumbers = this.transactions[0].bidderNumber.split('-').map(Number)
      },
      async fetchExhibitor() {
        // fetches the current exhibitor by its sale number
        let url = `http://${process.env.HOST_NAME}:8081/exhibitor/saleNumber/${this.saleNumber}`
        await this.axios.get(url).then(response => {
          this.exhibitor = response.data
        })
      },
      async fetchBuyers() {
        let uri = `http://${process.env.HOST_NAME}:8081/buyer`
        this.axios.get(uri).then(response => {
          this.buyers = response.data
        })
      },
      async fetchTransactions() {
        let url = `http://${process.env.HOST_NAME}:8081/transaction/saleNumber/${this.saleNumber}`
        try {
          await this.axios.get(url).then(response => {
            this.transactions = response.data
          })
        } catch (err) {
          console.log("Waiting for transactions to occur")
        }
      },
      async fetchAddons() {
        this.addons = []
        for (let i = 0; i < this.transactions.length; i++) {
          if (this.transactions[i].purchaseType == "Addon") {
            this.addons.push({
            name: this.buyers[this.transactions[i].bidderNumber].name,
            purchaseAmount: this.transactions[i].purchaseAmount
            })
          }
        }
        console.log(this.addons)
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

.table_c1 {
  color: #339966;
  font-size: 25px;
  width: 5%; }

.sale_number {
  text-align: center; }

.table_tail,
.table_c2,
.table_c3,
.table_c4 {
  width: 32%;
  color: #339966;
  font-size: 25px; }

.exhibitor_table_rows {
  font-size: 40px;  }

.buyer_rows {
  font-size: 35px;  }

.addon_rows {
  font-size: 20px;  }

</css>
