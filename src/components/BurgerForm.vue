<template>
  <div>
    <Message :msg="msg" v-show="msg" />
    <form id="burger-form" method="POST" @submit="createBurger">
      <div class="input-container">
        <label for="nome">Nome do cliente:</label>
        <input type="text" id="nome" name="nome" v-model="nome" placeholder="Digite o seu nome">
        <p class="erro none" id="erroNome">Por favor, preencha este campo!</p>
      </div>
      <div class="input-container">
        <label for="pao">Escolha o pão:</label>
        <select name="pao" id="pao" v-model="pao">
          <option value="">Selecione o seu pão</option>
          <option v-for="pao in paes" :key="pao.id" :value="pao.tipo">{{ pao.tipo }}</option>
        </select>
        <p class="erro none" id="erroPao">Por favor, preencha este campo!</p>
      </div>
      <div class="input-container">
        <label for="carne">Escolha a carne do seu Burger:</label>
        <select name="carne" id="carne" v-model="carne">
          <option value="">Selecione o tipo de carne</option>
          <option v-for="carne in carnes" :key="carne.id" :value="carne.tipo">{{ carne.tipo }}</option>
        </select>
        <p class="erro none" id="erroCarne">Por favor, preencha este campo!</p>
      </div>
      <div id="opcionais-container" class="input-container">
        <label id="opcionais-title" for="opcionais">Selecione os opcionais:</label>
        <div class="checkbox-container" v-for="opcional in opcionaisdata" :key="opcional.id">
          <input type="checkbox" name="opcionais" v-model="opcionais" :value="opcional.tipo">
          <span>{{ opcional.tipo }}</span>
        </div>
      </div>
      <div class="input-container">
        <input class="btn" type="button" v-on:click="validaForm()" value="Criar meu Burger!">
        <input class="submit-btn none" type="submit" id="botaoConfirmar" value="Criar meu Burger!">
      </div>
    </form>
  </div>
</template>

<script>
import Message from './Message'

export default {
  name: "BurgerForm",
  data() {
    return {
      paes: null,
      carnes: null,
      opcionaisdata: null,
      nome: null,
      pao: null,
      carne: null,
      opcionais: [],
      status: "Solicitado",
      msg: null
    }
  },
  methods: {
    async getIngredientes() {
      const req = await fetch('http://localhost:3000/ingredientes')
      const data = await req.json()

      this.paes = data.paes
      this.carnes = data.carnes
      this.opcionaisdata = data.opcionais
    },
    validaForm () {
      var nome = document.querySelector('#nome').value
      var pao = document.querySelector('#pao').value
      var carne = document.querySelector('#carne').value
      let erro = 0
      if (nome == '') {
        document.querySelector('#erroNome').classList.remove('none')
        erro = 1
      } else {
        document.querySelector('#erroNome').classList.add('none')
      }
      if (pao == '') {
        document.querySelector('#erroPao').classList.remove('none')
        erro = 1
      } else {
        document.querySelector('#erroPao').classList.add('none')
      }
      if (carne == '') {
        document.querySelector('#erroCarne').classList.remove('none')
        erro = 1
      } else {
        document.querySelector('#erroCarne').classList.add('none')
      }
      if (erro == 1) {
        return false
      } else {
        document.getElementById('botaoConfirmar').click()
      }
    },
    async createBurger(e) {

      e.preventDefault()

      const data = {
        nome: this.nome,
        carne: this.carne,
        pao: this.pao,
        opcionais: Array.from(this.opcionais),
        status: "Solicitado"
      }

      const dataJson = JSON.stringify(data)    

      const req = await fetch("http://localhost:3000/burgers", {
        method: "POST",
        headers: { "Content-Type" : "application/json" },
        body: dataJson
      });

      const res = await req.json()

      console.log(res)

      this.msg = "Pedido realizado com sucesso!"

      // clear message
      setTimeout(() => this.msg = "", 4000)

      // limpar campos
      this.nome = ""
      this.carne = ""
      this.pao = ""
      this.opcionais = []
      
    }
  },
  mounted () {
    this.getIngredientes()
  },
  components: {
    Message
  }
}
</script>

<style scoped>
  #burger-form {
    max-width: 400px;
    margin: 0 auto;
  }

  .erro {
    color: red;
    font-weight: bold;
    margin-top: 5px;
  }

  #nome {
    border-style: inset;
  }

  .none {
    display: none;
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

  #opcionais-container {
    flex-direction: row;
    flex-wrap: wrap;
  }

  #opcionais-title {
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

  .btn {
    background-color: #222;
    color:#fcba03;
    font-weight: bold;
    border: 2px solid #222;
    padding: 10px;
    font-size: 16px;
    margin: 2 auto;
    cursor: pointer;
    transition: .5s;
  }

  .btn:hover {
    background-color: transparent;
    color: #222;
  }
</style>