<template>
  <v-main>
    <v-container fluid>
      <v-row>
        <v-col align="center" justify="center" cols="12">
          <blockquote>
            &#8220;Welcome {{validUserName}}!&#8221;
            <footer>
              <small>
                <em>&mdash;Eagle Financial Services, your Midwest Financial Services Partner.</em>
              </small>
            </footer>
          </blockquote>
        </v-col>
      </v-row>
    </v-container>

    <v-container fluid>
      <v-row
        align="center"
        justify="center"
      >
        <v-col 
          cols="12"
            sm="10"
            md="8"
        >
          <v-card class="elevation-2">
            <v-container
              fluid
              grid-list-md
            >
              <v-row>
                <v-col>
                  <v-card>
                    <v-img
                      :src="require('@/assets/images/customer.jpg')" height="200px">
                    </v-img>
                    <v-card-text>
                      <div class="blue--text text-h6">
                        Customers
                      </div>  
                    </v-card-text>
                    <v-card-actions>
                      <v-btn flat color="orange" @click="viewCustomers">View Details</v-btn>
                    </v-card-actions>
                  </v-card>
                </v-col>
                <v-col>
                  <v-card>
                     <v-img
                      :src="require('@/assets/images/nonstock.jpg')" height="200px">
                    </v-img>
                    <v-card-text>
                      <div class="blue--text text-h6">
                        Investments
                      </div>  
                    </v-card-text>
                    <v-card-actions>
                      <v-btn flat color="orange" @click="viewInvestments">View Details</v-btn>
                    </v-card-actions>
                  </v-card>
                </v-col>
                <v-col>
                  <v-card>
                    <v-img
                      :src="require('@/assets/images/stocks.jpg')" height="200px">
                    </v-img>
                    <v-card-text>
                      <div class="blue--text text-h6">
                        Stocks
                      </div>  
                    </v-card-text>
                    <v-card-actions>
                      <v-btn flat color="orange" @click="viewStocks">View Details</v-btn>
                    </v-card-actions>
                  </v-card> 
                </v-col>
              </v-row>
            </v-container>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </v-main>
</template>

<script>
  import router from '../router';

  export default {
    name: 'Home',
    data: () => ({
      validUserName: "Guest"
    }),
    mounted() {
      this.getUser();
    },
    methods: {
      viewCustomers() {
        router.push('/customer-list');
      },
      viewInvestments() {
        router.push('/investment-list');
      },
      viewStocks() {
        router.push('/stock-list');
      },
      getUser() {
        if (localStorage.getItem("isAuthenticates")
          && JSON.parse(localStorage.getItem("isAuthenticates")) === true) {
          this.validUserName = JSON.parse(localStorage.getItem("log_user"));
        }
      }
    }
  }
</script>
