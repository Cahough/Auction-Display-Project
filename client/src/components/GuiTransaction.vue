<template>
  <div class="alternative_transaction">
    <router-link v-bind:to="{ name: 'Admin' }">
      <button class="transaction__top-button--yellow"><span class="button-arrow">&#8592;</span> Return to Dashboard</button>
    </router-link>
    <main>
      <div class="grid-container">
        <div class="current_sale_form">
          <div class="green_border">
            <button class="form__button" @click="displayCurrentExhibitor">Submit To Current Sale</button>
            <p class="input-field__label">Sale Number</p>
            <input v-validate="'required|numeric'" type="number" v-on:input="getExhibitorBySaleNum" name="saleNumber" v-model="saleNumber">
            <label class="errorLabel" for="saleNumber">{{ errors.first('saleNumber') }}</label>
            <label class="input__name-label">Exhibitor Name: {{ exhibitorName }}</label>
          </div>
          <div class="green_border">
            <section class="form__section">
              <p class="input-field__label">Buyer Bidder Number</p>
              <input @focus="clearBidder" @blur="zeroBidder" v-validate="'required|numeric'" type="number" v-on:input="getBuyerByBidderNum" name="bidderNumber" v-model="bidderNumber">
              <label class="errorLabel" for="saleNumber" >{{ errors.first('bidderNumber') }}</label>
              <label class="input__name-label">Buyer Name: {{ buyerName }}</label>
              <button class="form__button--second" @click="addNewBidder">Add Bidder</button>
            </section>
          </div>
          <div class="green_border">
            <section class="form__section">
              <p class="input-field__label">Addon Bidder Number</p>
              <span class="warning_label" v-if="saleNumber != display.saleNumber"> &#9888;</span>
              <input @focus="clearAddon" @blur="zeroAddon" v-validate="'required|numeric'" type="number" v-on:input="getAddonByBidderNum" name="addonNumber" v-model="addonNumber">
              <label class="errorLabel" for="saleNumber" >{{ errors.first('addonNumber') }}</label>
              <label class="input__name-label">Buyer Name: {{ addonName }}</label>
              <p class="input-field__label">Amount</p>
              <label class="errorLabel" for="addonPurchaseAmount" >{{ errors.first('addonPurchaseAmount') }}</label>
              <input @focus="clearPurchase" @blur="zeroPurchase" v-validate="'required|numeric'" type="number" name="addonPurchaseAmount" v-model="addonPurchaseAmount">
              <button class="form__button--second" name="addBtn" @click="addNewAddonTransaction">Submit Addon</button>
            </section>
          </div>
        </div>
        <div class="current_sale_view">
          <div class="current_exhibitor">
            <p>Current Sale:</p>
            <p v-if="previousSaleNumber != exhibitor.saleNumber">{{exhibitor.fullName}}</p>
            <p v-else>Waiting for New Exhibitor</p>
            <p v-if="exhibitor != null && previousSaleNumber != exhibitor.saleNumber">{{exhibitor.saleNumber}}</p>
            <p v-else>#</p>
          </div>
          <div class="current_buyers">
            <table class="current_exhibitor_buyers">
              <thead>Buyers</thead>
              <tbody>
                <tr v-for="b in bidders" :key="b">
                  <td>{{ buyers[b-1].name }}</td>
                  <td class="clickable" @click="deleteBidder(b)">Delete</td>
                </tr>
              </tbody>
            </table>
          </div>
          <div class="current_addons">
            <table>
              <thead>Addons</thead>
              <tbody v-if="previousSaleNumber != exhibitor.saleNumber">
                <tr v-if="addon.column == 1" v-for="addon in addons" :key="addon._id">
                  <td>{{ addon.name }}</td>
                  <td>${{ addon.purchaseAmount }}</td>
                  <td class="clickable" @click="deleteAddon(addon._id)">Delete</td>
                </tr>
              </tbody>
            </table>
          </div>
          <div class="current_addons2">
            <table>
              <thead>Addons</thead>
              <tbody v-if="previousSaleNumber != exhibitor.saleNumber">
                <tr v-if="addon.column == 2" v-for="addon in addons" :key="addon._id">
                  <td>{{ addon.name }}</td>
                  <td>${{ addon.purchaseAmount }}</td>
                  <td class="clickable" @click="deleteAddon(addon._id)">Delete</td>
                </tr>
              </tbody>
            </table>
          </div>
          <div class="button_holder">
            <table>
              <tr>
                <td></td>
                <td><label class="errorLabel" for="purchaseAmount" >{{ errors.first('purchaseAmount') }}</label></td>
              </tr>
              <tr>
                <td>Purchase Amount:</td>
                <td><input v-validate="'required|numeric'" type="number" name="purchaseAmount" v-model="purchaseAmount"></td>
                <td><button class="temp_button" name="addBtn" @click="addNewBuyerTransaction">Submit & Go To Next Sale</button></td>
              </tr>
            </table>
          </div>
        </div>
        <div class="previous_sale_view">
          <div class="previous_exhibitor">
            <p>Previous Sale:</p>
            <p>Waiting for transactions</p>
            <p>#</p>
          </div>
          <div class="previous_buyers">
            <table>
              <thead>Buyers</thead>
              <tr><td>b 1</td><td>Delete</td></tr>
              <tr><td>b 2</td><td>Delete</td></tr>
              <tr><td>b 3</td><td>Delete</td></tr>
              <tr><td>b 4</td><td>Delete</td></tr>
              <tr class="previous_table_tail">
                <td>Purchase Amount: 100</td><td>Edit</td>
              </tr>
            </table>
          </div>
          <div class="previous_addons">
            <table>
              <thead>Addons</thead>
              <tr><td>a 1</td><td>$10</td><td>Delete</td></tr>
              <tr><td>a 2</td><td>$10</td><td>Delete</td></tr>
              <tr><td>a 3</td><td>$10</td><td>Delete</td></tr>
              <tr><td>a 4</td><td>$10</td><td>Delete</td></tr>
            </table>
          </div>
          <div class="previous_addons2">
            <table>
              <thead>Addons</thead>
              <tr><td>a 5</td><td>$10</td><td>Delete</td></tr>
              <tr><td>a 6</td><td>$10</td><td>Delete</td></tr>
              <tr><td>a 7</td><td>$10</td><td>Delete</td></tr>
              <tr><td>a 8</td><td>$10</td><td>Delete</td></tr>
            </table>
          </div>
          <div class="button_holder">
            <table>
              <tr>
                <td>Change View: </td>
                <td><input class="change_view_input"></td>
                <td><button class="temp_button2">Submit</button></td>
              </tr>
            </table>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
  import {mapState, mapActions} from 'vuex'
  export default {
    name: 'GuiTransaction',
    data () {
      return {
        saleNumber: 0, // Active number to submit or add to
        previousSaleNumber: 0,
        bidderNumber: 0, // To submit new buyer
        addonNumber: 0, // To submit new addon
        addonPurchaseAmount: 0, // To submit with addonNumber
        purchaseAmount: 0, // To submit to current exhibitor with bidders[]
        buyerName: "",
        addonName: "",
        exhibitorName: "",
        showCurrentSale: false,
        showPreviousSale: false,
        currentSaleCheck: false,
        previousSaleCheck: false,
        display: [],
        bidders: [], // To submit to current exhibitor
        exhibitor: [], // Current display exhibitor with purchaseAmount
        previousExhibitor: [],
        buyerNumbers: [], // Current display buyers
        buyers: [], // Complete buyers list
        addons: [], // Current exhibitor addons list
        addons2: [], // Previous exhibitor addons list
        transactions: [], // Current exhibitor transactions list
        transactions2: [] // Previous exhibitor transactions list
      }
    },
    created: function () {
      this.fetchData()
    },
    computed: {
      ...mapState({
        // saleNumber: state => state.saleNumber,
        // transactions: state => state.transactions
      })
    },
    methods: {
      async fetchData() {
        this.fetchPreviousExhibitor()
        this.fetchBuyers()
        this.fetchExhibitor()
        this.fetchTransactions()
        await this.fetchDisplay()

        this.saleNumber = this.display.saleNumber
      },
      async fetchDisplay() {
        let url = `http://${process.env.HOST_NAME}:8081/display`
        await this.axios.get(url).then(response => {
          if (response.data.length >= 1) {
            this.display = response.data[0]
            this.fetchExhibitor(response.data[0].saleNumber)
          }
        })
        this.fetchTransactions()
      },
      async displayCurrentExhibitor() {
        // sets flag to display the current sale
        this.showCurrentSale = true
        // this.showCurrentSaleSection = true
        let state = {
          saleNumber: this.saleNumber,
          previousSaleNumber: this.previousSaleNumber,
          showCurrentSale: this.showCurrentSale,
          showPreviousSale: this.showPreviousSale
        }
        let uri = `http://${process.env.HOST_NAME}:8081/display/${this.display._id}`
        await this.axios.put(uri, state).then((response) => { })
        this.fetchData()
      },
      async addNewBuyerTransaction() {
        if (this.purchaseAmount == 0) {
          window.alert("Purchase amount cannot be 0")
        } else {
          // checks how many bidders there are, joins it if there's more than one
          if (this.bidders.length > 1) this.bidders = this.bidders.join('-')
          else this.bidders = this.bidders.toString()

          let newTransaction = {
            saleNumber: this.display.saleNumber,
            bidderNumber: this.bidders,
            purchaseAmount: this.purchaseAmount,
            purchaseType: "Buyer"
          }
          let uri = `http://${process.env.HOST_NAME}:8081/transaction/add`

          // Attempts post to db, on fail tries again
          await this.axios.post(uri, newTransaction)
            .then((response) => { console.log(response) })
            .catch((err) => {
              window.alert(
                "Failed with:\n" +
                err +
                "\nsaleNumber: " + this.display.saleNumber +
                "\nbidderNumber: " + this.bidders +
                "\npurchaseAmount: " + this.purchaseAmount)
            })

          // sets flag to show previous sale
          this.showPreviousSale = true
          let state = {
            saleNumber: this.saleNumber,
            previousSaleNumber: this.previousSaleNumber,
            showCurrentSale: this.showCurrentSale,
            showPreviousSale: this.showPreviousSale
          }
          await this.axios.put(`http://${process.env.HOST_NAME}:8081/display/` + this.display._id, state)
            .then(response => { console.log(response) })
            .then(() => {
              this.bidders = []
              this.addons = []
              this.saleNumber++
              this.fetchPreviousExhibitor()
            })
        }
      },
      async addNewAddonTransaction() {
        if (this.addonNumber != 0) {
          let newTransaction = {
            saleNumber: this.saleNumber,
            bidderNumber: this.addonNumber,
            purchaseAmount: this.addonPurchaseAmount,
            purchaseType: "Addon"
          }
          let uri = `http://${process.env.HOST_NAME}:8081/transaction/add`
          await this.axios.post(uri, newTransaction)
          .then((response) => {
            console.log(response)
            this.fetchTransactions()
          })
          .then(() => {
            this.addonNumber = 0
            this.addonPurchaseAmount = 0
          })
        } else {
          window.alert("Addon number cannot be 0")
        }
      },
      async addNewBidder() {
        // pushes to array if there's more than on bidder number [x]
        // has a confirmation message that it's been added to the table [ ]
        if (this.bidders.indexOf(this.bidderNumber) > -1) {
          window.alert("Bidder already present!")
        } else if (this.bidderNumber == 0) {
          window.alert("Bidder number cannot be 0")
        } else {
          this.bidders.push(this.bidderNumber)
          console.log(this.bidders)
        }
        // resets input field
        this.bidderNumber = "0"
      },
      async getExhibitorBySaleNum() {
        let uri = `http://${process.env.HOST_NAME}:8081/exhibitor/saleNumber/${this.saleNumber}`
        await this.axios.get(uri).then(response => {
          if (response.data == null) {
            this.exhibitorName = `Exhibitor with sale number ${this.saleNumber} does not exist.`
          }
          else {
            this.exhibitorName = response.data.fullName
          }
        })
      },
      async fetchExhibitor(saleNumber) {
        // fetches the current exhibitor by its sale number
        let url = `http://${process.env.HOST_NAME}:8081/exhibitor/saleNumber/${saleNumber}`
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
      async getBuyerByBidderNum() {
        let uri = `http://${process.env.HOST_NAME}:8081/buyer/bidderNumber/${this.bidderNumber}`
        await this.axios.get(uri).then(response => {
          if (response.data == null) {
            this.buyerName = `Buyer with bidder number ${this.bidderNumber} does not exist.`
          }
          else {
            this.buyerName = response.data.name
          }
        })
      },
      async getAddonByBidderNum() {
        let uri = `http://${process.env.HOST_NAME}:8081/buyer/bidderNumber/${this.addonNumber}`
        await this.axios.get(uri).then(response => {
          if (response.data == null) {
            this.addonName = `Buyer with bidder number ${this.addonNumber} does not exist.`
          }
          else {
            this.addonName = response.data.name
          }
        })
      },
      async fetchTransactions() {
        let url = `http://${process.env.HOST_NAME}:8081/transaction/saleNumber/${this.display.saleNumber}`
        await this.axios.get(url).then(response => {
          this.transactions = response.data
        })
        .catch(() => console.log("Waiting for transaction to occur"))

        this.fetchAddons()
        this.fetchBuyerNumbers()
      },
      async fetchBuyers() {
        let uri = `http://${process.env.HOST_NAME}:8081/buyer`
        this.axios.get(uri).then(response => {
          this.buyers = response.data
        })
      },
      async sortBuyers() {
        this.buyers = this.buyers.sort((a, b) => a.bidderNumber - b.bidderNumber)
      },
      async fetchAddons() {
        this.addons = []
        await this.sortBuyers()

        let col = 1
        for (let i = 0; i < this.transactions.length; i++) {
          if (this.transactions[i].purchaseType == "Addon") {
            col = Math.floor(i / 11 + 1)
            this.addons.push({
            _id: this.transactions[i]._id,
            name: this.buyers[this.transactions[i].bidderNumber - 1].name,
            purchaseAmount: this.transactions[i].purchaseAmount,
            column: col
            })
          }
        }
      },
      deleteBidder(bidderNumber) {
        this.bidders.splice(this.bidders.indexOf(bidderNumber), 1)
      },
      clearBidder() {
        this.bidderNumber = this.bidderNumber == 0 ? '' : this.bidderNumber
      },
      zeroBidder() {
        this.bidderNumber = this.bidderNumber == '' ? 0 : this.bidderNumber
      },
      clearAddon() {
        this.addonNumber = this.addonNumber == 0 ? '' : this.addonNumber
      },
      zeroAddon() {
        this.addonNumber = this.addonNumber == '' ? 0 : this.addonNumber
      },
      clearPurchase() {
        this.addonPurchaseAmount = this.addonPurchaseAmount == 0 ? '' : this.addonPurchaseAmount
      },
      zeroPurchase() {
        this.addonPurchaseAmount = this.addonPurchaseAmount == '' ? 0 : this.addonPurchaseAmount
      },
      async deleteBuyer(id) {
        console.log("Delete Buyer Reached")
      },
      async deleteAddon(id) {
        console.log(id)
        let uri = `http://${process.env.HOST_NAME}:8081/transaction/` + id
        await this.axios.delete(uri).then((response) => {
          console.log(response)
        })
        this.fetchTransactions()
      },
      async fetchBuyerNumbers() {},
      ...mapActions(['setSaleNumber', 'setTransactions'])
    }
  }
</script>

<css>
.current_exhibitor_buyers {
  text-align: left;
}
.warning_label {
  color: #fadc23;
  font-size: 30px;
}
.warning_label:after{
  content: &#9888;
}
.warning_label:hover:after{
  font-size: 20px;
  content: 'Sale Number is not = to Current Sale';
}
.clickable:hover {
  cursor: pointer;
  color: #fadc23;
  text-decoration: underline; }

.temp_button {
  background-color: #fadc23;
  color: green;
  margin-bottom: 15px;
  padding: 10px 15px;
}

.temp_button2 {
  color: green;
  margin-bottom: 15px;
  padding: 10px 15px;
  background-color: #f1f1f1; }

.change_view_input {
  border: 1px solid green; }

.green_border {
  padding: 10px;
  margin: 10px;
  border: 1px solid green;  }

.alternative_transaction {
  text-align: center;
  margin: 10px;
}
.current_sale_form {
  border: 1px solid green;
  padding: 10px;
  margin: 20px; }

.current_sale_view {
  height: 43vh;
  margin: 20px;
  color: #ffffff;
  background-color: #339966;  }

.previous_sale_view {
  color: #339966;
  border: 1px solid #339966;
  margin: 20px; }

.previous_exhibitor, .current_exhibitor {
  font-size: 30px;
}
.grid-container {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  grid-template-rows: 1fr 1fr 1fr 1fr;
  grid-template-areas: "current_sale_form current_sale_view current_sale_view current_sale_view" "current_sale_form current_sale_view current_sale_view current_sale_view" "current_sale_form previous_sale_view previous_sale_view previous_sale_view" "current_sale_form previous_sale_view previous_sale_view previous_sale_view";
}

.current_sale_form { grid-area: current_sale_form; }

.current_sale_view {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  grid-template-rows: 1fr 1fr 1fr 1fr 1fr;
  grid-template-areas: "current_exhibitor current_buyers current_addons current_addons2" "current_exhibitor current_buyers current_addons current_addons2" "current_exhibitor current_buyers current_addons current_addons2" "current_exhibitor current_buyers current_addons current_addons2" "button_holder button_holder button_holder button_holder";
  grid-area: current_sale_view;
}

.current_exhibitor { grid-area: current_exhibitor; }

.current_buyers { grid-area: current_buyers; }

.current_addons { grid-area: current_addons; }

.button_holder { grid-area: button_holder; }

.current_addons2 { grid-area: current_addons2; }

.previous_sale_view {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  grid-template-rows: 1fr 1fr 1fr 1fr 1fr;
  grid-template-areas: "previous_exhibitor previous_buyers previous_addons previous_addons2" "previous_exhibitor previous_buyers previous_addons previous_addons2" "previous_exhibitor previous_buyers previous_addons previous_addons2" "previous_exhibitor previous_buyers previous_addons previous_addons2" "button_holder button_holder button_holder button_holder";
  grid-area: previous_sale_view;
}

.previous_exhibitor { grid-area: previous_exhibitor; }

.previous_buyers { grid-area: previous_buyers; }

.previous_addons { grid-area: previous_addons; }

.button_holder { grid-area: button_holder; }

.previous_addons2 { grid-area: previous_addons2; }
</css>
