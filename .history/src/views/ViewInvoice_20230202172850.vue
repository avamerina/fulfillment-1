<template>
    <div class="view-invoice">
        <div class="view-invoice__inner">
            <v-container>
                <router-link to="/invoices">
                    <v-btn
                        class="mt-3 mb-5"     
                        color="grey"
                        dark
                        >
                            <v-icon
                            dark
                            left
                            >
                            mdi-arrow-left
                            </v-icon>Назад
                        </v-btn>
                    </router-link>
                <v-row>
                    <v-col cols="6">
                        <v-card
                        elevation="7"
                        shaped
                        >
                            <v-col class="mt-3 ml-3" style="display:flex; justify-content: space-between;">
                                <div class="">
                                    <h3>Накладная № {{invoiceNumber}}</h3>
                                    <p>Дата накладной: {{invoiceDate}}</p>
                                </div>
                                <div class="mr-3">
                                    <h3 v-if="role == 'Admin_ff'">Организация: {{organization}}</h3>
                                </div>
                            </v-col>
                        </v-card>
                    </v-col>
                    <v-col v-if="role == 'Client'">
                        <form @submit.prevent="sendFile()" action="">
                            <v-row class="mt-5">
                                <v-col>
                                    <v-file-input
                                        
                                        id="file"
                                        ref="file"
                                        v-on:change="handleFileUpload"
                                        accept=".xlsx"
                                        label="Загрузить Excel"
                                    ></v-file-input>
                                </v-col>

                                
                                <v-col>
                                    
                                    <v-btn
                                    dark
                                    type="submit"
                                    color="green"
                                    >
                                    <v-icon
                                    dark
                                    
                                    >
                                    mdi-checkbox-marked-circle
                                    </v-icon>
                                        Загрузить
                                    </v-btn>
                                </v-col>
                                <p class="invalid-feedback" v-if="error">{{error}}</p>
                                
                                <v-col></v-col>
                            </v-row>
                        </form>
                    </v-col>
                </v-row>
            </v-container>
            <v-simple-table>
                <template v-slot:default>
                <thead>
                    <tr>
                        <!-- <th class="text-left">
                            Накладная
                        </th> -->
                        <th class="text-left">
                            ID
                        </th>
                        <th class="text-left">
                            Наименование
                        </th>
                        <th class="text-left">
                            Артикул
                        </th>
                        <th class="text-left">
                            шт.
                        </th>
                        <th class="text-left">
                            Коробок
                        </th>
                        <th class="text-left">
                            Кол-во в коробке
                        </th>
                        <th class="text-left">
                            Общий вес коробки
                        </th>
                        <th class="text-left">
                            Вес 1 шт. 
                        </th>
                        <th class="text-left">
                            Штрих-код
                        </th>
                        <th class="text-left">
                            Цена НДС
                        </th>
                        <th class="text-left">
                            Высота, м
                        </th>
                        <th class="text-left">
                            Ширина, м
                        </th>
                        <th class="text-left">
                            Длина, м
                        </th>
                        <th class="text-left">
                            Объем, м3
                        </th>
                    </tr>
                </thead>
                <tbody>
                    <tr
                    v-for="good in goodList"
                    :key="good.id"
                    >
                        <!-- <td>{{ good.invoice_detail[0] }}</td> -->
                        <td>{{ good.id }}</td>
                        <td>{{ good.title }}</td>
                        <td>{{ good.vendor_code }}</td>
                        <td>{{ good.good_quantity }}</td>
                        <td>{{ good.box_quantity }}</td>
                        <td>{{ good.good_quantity_in_box }}</td>
                        <td>{{ good.box_full_weight }}</td>
                        <td>{{ good.good_unit_weight }}</td>
                        <td>{{ good.bar_code }}</td>
                        <td>{{ good.tax }}</td>
                        <td>{{ good.height_m }}</td>
                        <td>{{ good.width_m }}</td>
                        <td>{{ good.length_m }}</td>
                        <td>{{ good.capacity_m3 }}</td>
                    </tr>
                </tbody>
                </template>
            </v-simple-table>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
export default {
    data: () => ({
        file: '',
        invoiceNumber: '',
        invoiceDate: '',
        goodList: [],
        role: '',
        organization: '',
        error: ''
    }),
    methods:{
        
        sendFile(){
            let formData = new FormData();
            formData.append('file', this.file);
            formData.append('invoice', localStorage.getItem('invoiceId'))
            console.log(csrfToken)
            axios.post('http://87.255.194.27:8001/api/goods/download/',
            
                formData,
            
            {
                headers:{
                    Authorization: 'Token ' + localStorage.getItem('usertoken'),
                    // 'X-CSRF-TOKEN': csrfToken,
                    'Content-Type': 'multipart/form-data'
                }
            }) .then((response) => {
                console.log(response)
                alert('Успешно')
                this.getInvoiceGoods()
                this.$refs.file.reset()

            }).catch((error) => {
                console.log(error)

                
               

                if( error.response.data.error == "Data is not valid"){
                    this.error = "Cодержание не соответствует требуемого формата"
                }

                 else if(error.message == 'Request failed with status code 400'){
                    this.error = "Файл не прикреплен либо содержание не соответствует требуемого формата"
                }
                

                else if(error.message == 'Request failed with status code 500'){
                    this.error = "Файл не прикреплен либо содержание не соответствует требуемого формата"
                }
            
              
            })
            .then((response) => {
                console.log(response)
                alert('Успешно')
                this.getInvoiceGoods()
                this.$refs.file.reset()})
        },
        handleFileUpload: function(file){
            this.file = file;
        },
        getInvoiceGoods(){
            axios.get('http://87.255.194.27:8001/api/organization/invoice/' + localStorage.getItem('invoiceId'),
            {
                headers:{
                    Authorization: 'Token ' + localStorage.getItem('usertoken')
                }
            }).then((response) => {
                console.log(response.data)
                this.invoiceDate = response.data.date,
                this.invoiceNumber = response.data.number,
                this.goodList = response.data.goods,
                this.organization = response.data.organization.name
            })
        },
        getUserRole(){
            this.role = localStorage.getItem('user_role')
        }
    },
    mounted(){
        this.getUserRole(),
        this.getInvoiceGoods()
    }
}
</script>

<style lang="scss" scoped>
.invalid-feedback{
    color: rgb(252, 20, 20);
}
</style>