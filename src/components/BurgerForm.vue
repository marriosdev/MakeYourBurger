<template>
    <div id="form">
        <div >
            <Message v-bind:msg = "msgForm" v-if="msg"/>
        </div>
        <form action="" id="burger-form" @submit="createBurger">
            <div class="input-container">
                <label for="nome">Nome</label>
                <input id="nome" name="name" v-model="nome" placeholder="Digite seu nome" type="text">
            </div>
            <div class="input-container">
                <label for="pao">Escolha o pao: </label>
                <select name="pao" id="pao" v-model="pao">
                    <option v-bind:value="pao.tipo" v-for="pao in paes" v-bind:key="pao.id"> {{ pao.tipo }} </option>
                </select>
            </div>
            <div class="input-container">
                <label for="carne">Escolha sua carne: </label>
                <select name="carne" id="carne" v-model="carne">
                    <option v-bind:value="carne.tipo" v-for="carne in carnes" v-bind:key="carne.id"> {{ carne.tipo }} </option>
                </select>
            </div>

            <div class="input-container">
                <label for="pao" id="op-title">Seleciona os opcionais</label>
                <div class="checkbox-container" v-for="opcional in opcionaisdata" v-bind:key="opcional.tipo" >
                    <input type="checkbox" name="opcionais" id="" v-model="opcionais" v-bind:value="opcional.tipo">
                    <span>{{ opcional.tipo }} </span>
                </div>
            </div>
            <div class="input-container">
                <input type="submit" class="submit-btn"  value="Enviar">
            </div>
        </form>
    </div>
</template>

<script>
import Message from './Message.vue'

export default {
    name: "BurgerForm",
    components: {
        Message
    },
    data() {
        return {
            msgForm: "",
            msg: false,
            pedidoSet: false,
            carnes: null,
            paes: null,
            opcionaisdata: null,
            nome: null,
            carne: null,
            pao: null,
            opcionais: [],
            status: "Solicitado"
            }
        },
    methods: {
        async getIngrdientes() {
            const req = await fetch("http://localhost:3000/ingredientes")
            const data = await req.json()
            
            this.carnes = data.carnes
            this.paes = data.paes
            this.opcionaisdata  = data.opcionais
        },

        async createBurger(e) {
            e.preventDefault()
            const data = {
                nome: this.nome,
                carne: this.carne,
                pao: this.pao,
                opcionais: this.opcionais,
                status: "Solicitado"
            }

            const req = await fetch("http://localhost:3000/burgers", {
                method: "POST",
                headers: {"Content-Type": "application/json"},
                body: JSON.stringify(data)
            }) 

            const resposta = await req.json()
            
            // Mostrando mensagem
            this.msgForm = `Pedido N ${resposta.id} criado com sucesso!`
            this.msg = true

            // Limpando campos 

            this.nome = ""
            this.carne = ""
            this.pao = ""
            this.opcionais = []

            setTimeout(() => this.msg = false, 3000)
            
        }
    },
    mounted() {
        this.getIngrdientes()
    }
}
</script>

<style scoped>
    #burger-form{
        max-width: 400px;
        margin: 0 auto;
    }
    .input-container{
        display: flex;
        flex-direction: column;
        margin-bottom: 20px;
    }
    label {
        font-weight: bold;
        margin-bottom: 15px;
        color: #222;
        padding: 5px 10px;
        border-left: 4px solid #ffbb00 !important;
    }
    input, select {
        padding: 5px 10px;
        width: 300px;
    }

    #opctionais-container{
        flex-direction: row;
        flex-wrap: wrap;
    }
    #op-title{
        width: 100%;
    }
    .checkbox-container {
        display: flex;
        align-items: flex-start;
        width: 50%;
        margin-bottom: 20px;
    }
    .checkbox-container span, .checkbox-container input {
        width: auto;
    }
    .checkbox-container span{
        margin-left: 6px;
        font-weight: bold;
    }
    
    .submit-btn{
        padding: 10px;
        font-size: 16px;
        font-weight: bold;
        background-color: #222;
        color: #FCBA03;
        border: 2px solid #222;
        margin: 0 auto;
        cursor: pointer;
        transition: .5s;
    }
    .submit-btn:hover{
        background-color: transparent;
        color: #222;
    }
</style>