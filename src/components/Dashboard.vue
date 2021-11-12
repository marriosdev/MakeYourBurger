<template>
    <div class="burger-table">
        <div v-if="msgSee">
            <Message :msg="msg"/>
        </div>
        <div>
            <div id="burger-table-heading">
                <div class="order-id">#:</div>
                <div>Clientes: </div>
                <div>Pao: </div>
                <div>Carne: </div>
                <div>Opcionais: </div>
                <div>Acoes: </div>
            </div>
        </div>    
        <div id="burger-table-rows">
            <div class="burger-table-row" v-for="burger in burgers" v-bind:key="burger">
                <div class="order-number">{{ burger.id }}</div>
                <div>{{ burger.nome }}</div>
                <div>{{ burger.pao}} </div>
                <div>{{ burger.carne}}</div>
                <div>
                    <ul>
                        <li v-for="op in burger.opcionais" v-bind:key="op">
                            {{ op }}
                        </li>
                    </ul>
                </div>
                <div>
                    <select name="status" class="status" @change="updateBurger($event, burger.id)">
                        <option  v-bind:value="sta.tipo" v-for="sta in status" v-bind:key="sta.tipo" v-bind:selected="sta.tipo == burger.status">
                            {{sta.tipo}}
                        </option>
                    </select>
                    <button class="delete-btn" @click="deleteBurger(burger.id)">Cancelar</button>
                </div>
            </div>
        </div>
    </div>    
</template>

<script>
import Message from '../components/Message.vue'
export default {
    name: "Dashboard",
    components: {
            Message
    },
    data() {
        return {
            msgSee: false,
            msg: null,
            burgers: null,
            status: null
        }
    },
    methods: {
        async getPedidos() {
            // Get pedidos
            const request = await  fetch("http://localhost:3000/burgers")
            this.burgers = await request.json()

            
        },
        async getStatus(){
            // Get status
            const requestStatus = await fetch("http://localhost:3000/status")
            this.status = await requestStatus.json()
            console.log(this.status)
        },
        async deleteBurger(id){
            const request = await fetch(`http://localhost:3000/burgers/${id}`, {
                method: "DELETE"
            })
            this.getPedidos()
            this.msg = "Pedido cancelado com sucesso"
            this.msgSee = true 
            setTimeout(() => this.msgSee = false, 3000)
        },

        async updateBurger(event, id){
            const option = event.target.value

            const dataJson = JSON.stringify({status: option})

            const req = await fetch(`http://localhost:3000/burgers/${id}`, {
                method: "PATCH",
                headers: {"Content-Type" : "application/json"},
                body: dataJson
            })

            const resp = await req.json()
            this.msg = "Pedido atualizado com sucesso"
            this.msgSee = true
            setTimeout(() => this.msgSee = false, 3000)
        }
    },
    mounted()
    {
        this.getStatus()
        this.getPedidos()
    }
}
</script>

<style scoped>
    #burger-table{
        max-width: 1200px;
        margin: 0 auto;
    }
    #burger-table-heading,
    #burger-table-rows,
    .burger-table-row {
        display: flex;
        flex-wrap: wrap;
    }

    #burger-table-heading {
        font-weight: bold;
        padding:  12px;
        border-bottom: 3px solid #333;
    }

    #burger-table-heading div,
    .burger-table-row div {
        width: 19%;
    }

    .burger-table-row {
        width: 100%;
        padding: 12px;
        border-bottom: 1px solid #CCC;
    }

    #burger-table-heading .order-id,
    .burger-table-row .order-number
    {
        width: 5%;
    }

    select{
        padding: 12px 6px;
        margin-right: 12px;
    }

    .delete-btn{
        background-color: #222;
        color: #FCBA03;
        font-weight: bold;
        border: 2px solid #222;
        padding: 10px;
        font-size: 16px;
        margin: 0 auto;
        cursor: pointer;
        transition: .5s;
    }
    .delete-btn:hover{
        background-color: transparent;
        color:#222;
    }
</style>