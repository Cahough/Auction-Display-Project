<template>
  <div class="alternative_transaction">
    <router-link v-bind:to="{ name: 'Admin' }">
      <button class="transaction__top-button--yellow"><span class="button-arrow">&#8592;</span> Return to Dashboard</button>
    </router-link>
    <main>
      <div class="grid-container">
        <div class="current_sale_form">
          <p class="input-field__label">Sale Number</p>
          <input v-validate="'required|numeric'" type="number" v-on:input="getExhibitorBySaleNum" name="saleNumber" v-model="saleNumber">
          <label class="errorLabel" for="saleNumber" >{{ errors.first('saleNumber') }}</label>
          <label class="input__name-label">Exhibitor Name: {{ exhibitorName }}</label>
          <button class="form__button" @click="displayCurrentExhibitor">Submit Current Sale</button>
          <section class="form__section">
            <p class="input-field__label">Bidder Number</p>
            <input v-validate="'required|numeric'" type="number" v-on:input="getBuyerByBidderNum" name="bidderNumber" v-model="bidderNumber">
            <label class="errorLabel" for="saleNumber" >{{ errors.first('bidderNumber') }}</label>
            <label class="input__name-label">Buyer Name: {{ buyerName }}</label>
            <button class="form__button--second" @click="addNewBidder">Add Another Bidder</button>
          </section>
          <section class="form__section">
            <p class="input-field__label">Amount:</p>
            <label class="errorLabel" for="purchaseAmount" >{{ errors.first('purchaseAmount') }}</label>
            <input v-validate="'required|numeric'" type="number" name="purchaseAmount" v-model="purchaseAmount">
            <button class="form__button" name="addBtn" @click="addNewTransaction">Submit & Go To Next Sale</button>
          </section>
        </div>
        <div class="current_sale_view">
          <div class="current_exhibitor">
            <p>Current Exhibitor:</p>
            <p class="exhibitor_name">Andrew Vidwink</p>
          </div>
          <div class="current_exhibitor_buyers">
            <table>
              <thead>Buyers</thead>
              <tr><td>b 1</td><td>Delete</td></tr>
              <tr><td>b 2</td><td>Delete</td></tr>
              <tr><td>b 3</td><td>Delete</td></tr>
              <tr><td>b 4</td><td>Delete</td></tr>
              <tr class="current_table_tail">
                <td>Purchase Amount: 100</td>
              </tr>
            </table>
          </div>
          <div class="current_exhibitor_addons">
            <table>
              <thead>Addons</thead>
              <tr><td>a 1</td><td>$10</td><td>Delete</td></tr>
              <tr><td>a 2</td><td>$10</td><td>Delete</td></tr>
              <tr><td>a 3</td><td>$10</td><td>Delete</td></tr>
              <tr><td>a 4</td><td>$10</td><td>Delete</td></tr>
            </table>
          </div>
          <div class="current_exhibitor_addons2">
            <table>
              <thead>Addons</thead>
              <tr><td>a 5</td><td>$10</td><td>Delete</td></tr>
              <tr><td>a 6</td><td>$10</td><td>Delete</td></tr>
              <tr><td>a 7</td><td>$10</td><td>Delete</td></tr>
              <tr><td>a 8</td><td>$10</td><td>Delete</td></tr>
            </table>
          </div>
        </div>
        <div class="previous_sale_view">
          <div class="previous_exhibitor"></div>
          <div class="previous_buyers"></div>
          <div class="previous_addons"></div>
          <div class="previous_addons"></div>
        </div>
      </div>
    </main>
    <router-link v-bind:to="{ name: 'Admin' }">
      <button class="transaction__top-button--yellow"><span class="button-arrow">&#8592;</span> Delete ALL Transactions</button>
    </router-link>
  </div>
</template>

<css>
.alternative_transaction {
  text-align: center;
  margin: 10px;
}
.current_sale_form {
  border: 1px solid green;
  padding: 10px;
  margin: 20px; }

.current_sale_view {
  margin: 20px;
  color: #ffffff;
  background-color: #339966;  }

  .previous_sale_view {
    border: 1px solid #339966;
    margin: 20px; }

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
    grid-template-rows: 1fr 1fr;
    grid-template-areas: "current_exhibitor current_buyers current_addons current_addons" "current_exhibitor current_buyers current_addons current_addons";
    grid-area: current_sale_view;
  }

  .current_exhibitor { grid-area: current_exhibitor; }

  .current_buyers { grid-area: current_buyers; }

  .current_addons { grid-area: current_addons; }

  .current_addons { grid-area: current_addons; }

  .previous_sale_view {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr 1fr;
    grid-template-rows: 1fr 1fr;
    grid-template-areas: "previous_exhibitor previous_buyers previous_addons previous_addons" "previous_exhibitor previous_buyers previous_addons previous_addons";
    grid-area: previous_sale_view;
  }

  .previous_exhibitor { grid-area: previous_exhibitor; }

  .previous_buyers { grid-area: previous_buyers; }

  .previous_addons { grid-area: previous_addons; }

  .previous_addons { grid-area: previous_addons; }
</css>
