<template>
  <div>
    <Message :message="message" v-show="message"/>
    <form id="burger-form" @submit="createBurguer">
      <div class="input-container">
        <label for="name">Nome do cliente:</label>
        <input type="text" id="name" name="name" v-model="name" placeholder="Digite o seu nome">
      </div>
      <div class="input-container">
        <label for="bread">Escolha o pão:</label>
        <select name="bread" id="bread" v-model="bread">
          <option value="">Selecione o seu pão</option>
          <option v-for="bread in breads" :key="bread.id" :value="bread.type">{{bread.type}}</option>
        </select>
      </div>
      <div class="input-container">
        <label for="meat">Escolha a carne do seu Burger:</label>
        <select name="meat" id="meat" v-model="meat">
          <option value="">Selecione o tipo de carne</option>
          <option v-for="meat in meats" :key="meat.id" :value="meat.type">{{meat.type}}</option>
        </select>
      </div>
      <div id="optional-container" class="input-container">
        <label id="optional-title" for="optional">Selecione os opcionais:</label>
        <div class="checkbox-container" v-for="optional in optionalData" :key="optional.id">
          <input type="checkbox" name ="optional" v-model="optionalItem" :value="optional.type">
          <span>{{optional.type}}</span>
        </div>
      </div>
      <div class="input-container">
        <input class="submit-btn" type="submit" value="Criar meu Burger!">
      </div>
    </form>
  </div>
</template>

<script>
import Message from './Message.vue'

export default {
    name: "BurgerForm",
    components: {
        Message,
    },
    data(){
        return{
            breads: null,
            meats: null,
            optionalData: null,
            name: null,
            bread: null,
            meat: null,
            optionalItem: [],
            message: null
        }
    },
    methods: {
        async getIngredients(){
            const req = await fetch("http://localhost:3000/ingredients")
            const data = await req.json()
            this.breads = data.breads;
            this.meats = data.meats;
            this.optionalData = data.optional
        },
        async createBurguer(e){
            e.preventDefault();
            const data = {
                name: this.name,
                meat: this.meat,
                bread: this.bread,
                optional: Array.from(this.optionalItem),
                status: "Solicitado",
            }
            const dataJson = JSON.stringify(data)
            const req = await fetch("http://localhost:3000/burgers", {
                method: "POST",
                headers: {"Content-type": "application/json"},
                body: dataJson
            })

            const res = await req.json()

            this.message = `Pedido Nº ${res.id} realizado com sucesso`

            setTimeout(() => this.message="", 3000);

            this.name = "";
            this.meat = "";
            this.bread = "";
            this.optionalItem = "";
        }
    },
    mounted(){
        this.getIngredients()
    }
}
</script>

<style scoped>
    #burger-form {
        max-width: 400px;
        margin: 0 auto;
    }

    .input-container {
        display: flex;
        flex-direction: column;
        margin-bottom: 20px;
    }

    label {
        font-weight: bold;
        margin-bottom: 15px;
        color: #222;;
        padding: 5px 10px;
        border-left: 4px solid #fcba03;
    }

    input, select {
        padding: 5px 10px;
        width: 300px;
    }

    #optional-container {
        flex-direction: row;
        flex-wrap: wrap;
    }

    #optional-title {
        width: 100%;
    }

    .checkbox-container {
        display: flex;
        align-items: flex-start;
        width: 50%;
        margin-bottom: 20px;
    }

    .checkbox-container span,
    .checkbox-container input {
        width: auto;
    }

    .checkbox-container span {
        margin-left: 6px;
        font-weight: bold;
    }

    .submit-btn {
        background-color: #222;
        color:#fcba03;
        font-weight: bold;
        border: 2px solid #222;
        padding: 10px;
        font-size: 16px;
        margin: 0 auto;
        cursor: pointer;
        transition: .5s;
    }

    .submit-btn:hover {
        background-color: transparent;
        color: #222;
    }
</style>