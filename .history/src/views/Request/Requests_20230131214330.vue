<template>
    <div class="requests">
            <h2 class="mt-5 ml-5">Список заявок</h2> <br>
            <router-link to="/request/1">
                <v-btn
                    v-if="role == 'Client'"
                    color="green"
                    dark
                    small
                    class="mt-5 ml-5"
                >
                    Добавить заявку
                </v-btn>
            </router-link>
            
            <v-simple-table>
                <template v-slot:default>
                <thead>
                    <tr>
                        <th class="text-left">
                            №
                        </th>
                        <th v-if="role == 'Admin_ff'"  class="text-left">
                            Организация
                        </th>
                        <th class="text-left">
                            Дата
                        </th>
                        <th class="text-left">
                            Получатель
                        </th>
                        <th class="text-left">
                            Адрес доставки
                        </th>
                        <th class="text-left">
                            Контакты
                        </th>
                        <th class="text-left">
                            Тип доставки 
                        </th>
                        <th v-if="role == 'Admin_ff'" class="text-left">
                            Штрих-код 
                        </th>
                        <th v-if="role == 'Admin_ff'" class="text-left">
                            Ячейка
                        </th>
                        <th class="text-left">
                            Статус доставки 
                        </th>
                        <th v-if="role == 'Admin_ff'" class="text-left">
                            Объем посылки
                        </th>
                        <th v-if="role == 'Client'" class="text-left">
                            Статус
                        </th>
                        <th class="text-left">
                            Страница заявки
                        </th>
                        
                    </tr>
                </thead>
                
                <tbody>
                    <tr
                    v-for="(order, index) in ordersList"
                    :key="order.id"
                    class="menu-row"
                    >
                        <td>{{ index + 1 }}</td>
                        <td v-if="role == 'Admin_ff'">{{ order.organization }}</td>
                        <td>{{ order.date }}</td>
                        <td>{{ order.recipient }}</td>
                        <td>{{ order.shipping_address }}</td>
                        <td>{{ order.contacts }}</td>
                        <td>{{ order.shipping_type }}</td>
                        <td v-if="role == 'Admin_ff'">{{ order.bar_code }}</td>
                        <td v-if="role == 'Admin_ff'">{{ order.cell_number }}</td>
                        <td>{{ order.status }}</td>
                        <td v-if="role == 'Admin_ff'">{{ order.package }}</td>
                        <td v-if="role == 'Client'">
                            <p style="color: red;" v-if="order.is_draft == true">Черновик</p>
                            <p style="color: green;" v-if="order.is_draft == false">Отправлено</p>
                        </td>
                        <td>
                            <router-link :to="{name: 'requests-view', params: {id: order.id}}">
                                    <a @click="setIdToStorage(order.id)" href="">
                                        Перейти
                                    </a>
                            </router-link>
                        </td>
                        
                    </tr>
                </tbody>
                </template>
            </v-simple-table>
            
        
    </div>
</template>

<script>
import axios from 'axios'
export default {
    data: () => ({
        ordersList: [],
        status: '',
        role: ''
    }),
    methods: {
        getOrderList(){
            axios.get('http://87.255.194.27:8001/api/orders/list/',
            {
                headers:{
                    Authorization: 'Token ' + localStorage.getItem('usertoken')
                }
            }).then((response) => {
                console.log(response.data)
                this.ordersList = response.data
            })
        },
        setIdToStorage(value){
            localStorage.setItem('orderId', value)
        },
        getUserRole(){
            this.role = localStorage.getItem('user_role')
        }
    },
    mounted(){
        this.getUserRole(),
        this.getOrderList()
        // this.getStatusList()
    }
}
</script>

<style lang="scss" scoped>

</style>