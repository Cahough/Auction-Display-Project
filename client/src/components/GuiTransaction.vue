<template>
  <div class="transaction">
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
        <div class="tempGrid"></div>
      </div>
    </main>
    <router-link v-bind:to="{ name: 'Admin' }">
      <button class="transaction__top-button--yellow"><span class="button-arrow">&#8592;</span> Delete ALL Transactions</button>
    </router-link>
  </div>
</template>

<css>
.current_sale_form {
  border: 1px solid green;
  padding: 10px;
  margin: 20px; }

.current_sale_view {
  margin: 20px;
  color: #ffffff;
  background-color: #339966;  }

  .grid-container {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr;
  grid-template-rows: 1fr 1fr 1fr;
  grid-template-areas: "current_sale_form current_sale_view current_sale_view current_sale_view current_sale_view" "tempGrid tempGrid tempGrid tempGrid tempGrid" "tempGrid tempGrid tempGrid tempGrid tempGrid";
}

.current_sale_form { grid-area: current_sale_form; }

.current_sale_view {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  grid-template-rows: 1fr;
  grid-template-areas: "current_exhibitor current_exhibitor_buyers current_exhibitor_addons current_exhibitor_addons2";
  grid-area: current_sale_view;
}

.current_exhibitor { grid-area: current_exhibitor; }

.current_exhibitor_buyers { grid-area: current_exhibitor_buyers; }

.current_exhibitor_addons { grid-area: current_exhibitor_addons; }

.current_exhibitor_addons2 { grid-area: current_exhibitor_addons2; }

.tempGrid { grid-area: tempGrid; }
</css>
