<template>
  <v-main>
    <!-- Welcome title -->
    <v-container fluid>
      <v-row align="center" justify="center">
        <v-col cols="12" align="center" justify="center">
          <blockquote>
            Welcome {{validUserName}}!
            <footer>
              <small>
                <em>&mdash;Eagle Financial Services, your Midwest Financial Services Partner.</em>
              </small>
            </footer>
          </blockquote>
        </v-col>
        <v-col  cols="12" md="10" lg="10" align="center" justify="center">
          <v-alert v-if="showMsg === 'new'"
                   dismissible
                   :value="true"
                   type="success"
          >
            New customer has been added.
          </v-alert>
          <v-alert v-if="showMsg === 'update'" dismissible
                   :value="true"
                   type="success"
          >
           Customer information has been updated.
          </v-alert>
          <v-alert v-if="showMsg === 'deleted'" dismissible
                   :value="true"
                   type="success"
          >
            Selected Customer has been deleted.
          </v-alert>
        </v-col>
      </v-row>

      <!-- Data table -->
      <v-row align="center" justify="center">
        <v-col cols="12" md="10" v-resize="onResize">
            <v-data-table
              :headers="headers"
              :items="customers"
              class="elevation-1"
              style="max-height: 300px; overflow-y: auto"
              v-if="!isMobile"
            >
                    <template v-slot:item="props">
                      <tr>
                        <td align="left">{{ props.item.cust_number }}</td>
                        <td align="left">{{ props.item.name }}</td>
                        <td nowrap="true" align="left">{{ props.item.address }}</td>
                        <td nowrap="true" align="left">{{ props.item.city }}</td>
                        <td nowrap="true" align="left">{{ props.item.state }}</td>
                        <td nowrap="true" align="left">{{ props.item.zipcode }}</td>
                        <td nowrap="true" align="left">{{ props.item.email }}</td>
                        <td nowrap="true" align="left">{{ props.item.cell_phone }}</td>
                        <td align="center"><v-icon @click="updateCustomer(props.item)">mdi-pencil</v-icon></td>
                        <td align="center"><v-icon @click="deleteCustomer(props.item)">mdi-delete</v-icon></td>
                      </tr>  
                    </template>
              </v-data-table>
              <v-data-iterator 
                :items="customers"
                hide-default-footer
                v-else
              >
                <template v-slot:default="{ items, isExpanded, expand }">
                  <v-row>
                    <v-col
                      v-for="item in items"
                      :key="item.name"
                      cols="12"
                    >
                      <v-card>
                        <v-card-title class="pb-0 pt-0 pl-0">
                          <v-col cols="9" class="text-left body-2 text-truncate">{{ item.name }}</v-col>
                          <v-col cols="3" class="text-center">
                            <v-card-actions>
                              <v-icon @click="updateCustomer(item)" class="small">mdi-pencil</v-icon>
                              <v-icon @click="deleteCustomer(item)" class="small">mdi-delete</v-icon>
                              <v-icon @click.native="expand(item, !isExpanded(item))" class="small">mdi-dots-horizontal</v-icon>
                            </v-card-actions>
                          </v-col>
                        </v-card-title>
                        <v-divider></v-divider>

                        <v-list v-show="isExpanded(item)" dense>
                          <v-list-item>
                            <v-list-item-content>Address:</v-list-item-content>
                            <v-list-item-content class="align-end">{{ item.address }}
                                {{ item.city }}
                                {{ item.state }}
                                {{ item.zipcode }}
                            </v-list-item-content>
                          </v-list-item>
                          <v-list-item>
                            <v-list-item-content>Email:</v-list-item-content>
                            <v-list-item-content class="align-end">{{ item.email }}</v-list-item-content>
                          </v-list-item>
                          <v-list-item>
                            <v-list-item-content>Cell Phone:</v-list-item-content>
                            <v-list-item-content class="align-end">{{ item.cell_phone }}</v-list-item-content>
                          </v-list-item>
                        </v-list>
                      </v-card>
                    </v-col>
                  </v-row>
                </template>     
              </v-data-iterator>  
              <v-btn class="blue mt-4 white--text" @click="addNewCustomer">Add Customer</v-btn>  
        </v-col>  
      </v-row>
    </v-container>  
  </v-main>
</template>


<script>

  import router from '../router';
  import {APIService} from '../http/APIService';
  const apiService = new APIService();

  export default {
    name: "CustomerList",
    data: () => ({
      customers: [],
      validUserName: "Guest",
      customerSize: 0,
      showMsg: '',
      isMobile: false,
      headers: [
        {text: 'Customer Number', sortable: false, align: 'left',},
        {text: 'Name', sortable: false, align: 'left',},
        {text: 'Address', sortable: false, align: 'left',},
        {text: 'City', sortable: false, align: 'left',},
        {text: 'State', sortable: false, align: 'left',},
        {text: 'ZipCode', sortable: false, align: 'left',},
        {text: 'Email', sortable: false, align: 'left',},
        {text: 'Phone', sortable: false, align: 'left',},
        {text: 'Update', sortable: false, align: 'left',},
        {text: 'Delete', sortable: false, align: 'left',}
      ],

    }),
    mounted() {
      this.getCustomers();
      this.showMessages();
    },
    methods: {
      onResize() {
          if (window.innerWidth > 600)
            this.isMobile = false;
          else  
            this.isMobile = true;
        },
      showMessages(){
        console.log(this.$route.params.msg)
        if (this.$route.params.msg) {
          this.showMsg = this.$route.params.msg;
        }
      },
      getCustomers() {
        apiService.getCustomerList().then(response => {
          this.customers = response.data.data;
          this.customerSize = this.customers.length;
          if (localStorage.getItem("isAuthenticates")
            && JSON.parse(localStorage.getItem("isAuthenticates")) === true) {
            this.validUserName = JSON.parse(localStorage.getItem("log_user"));
          }
        }).catch(error => {
          if (error.response.status === 401) {
            localStorage.removeItem('isAuthenticates');
            localStorage.removeItem('log_user');
            localStorage.removeItem('token');
            router.push("/auth");
          }
        });
      },
      addNewCustomer() {
        if (localStorage.getItem("isAuthenticates")
          && JSON.parse(localStorage.getItem("isAuthenticates")) === true) {
          router.push('/customer-create');
        } else {
          router.push("/auth");
        }
      },
      updateCustomer(customer) {
        router.push('/customer-create/' + customer.pk);
      },
      deleteCustomer(customer) {
        apiService.deleteCustomer(customer.pk).then(response => {
          if (response.status === 204) {
            router.push('/customer-list/deleted/')
            this.getCustomers()
          }
        }).catch(error => {
          if (error.response.status === 401) {
            localStorage.removeItem('isAuthenticates');
            localStorage.removeItem('log_user');
            localStorage.removeItem('token');
            router.push("/auth");
          }
        });
      }
    }
  };
</script>	
