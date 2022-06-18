<template>
  <div id="burger-table" v-if="burgers">
    <div>
      <div id="burger-table-heading">
        <div class="order-id">#:</div>
        <div>Cliente:</div>
        <div>Pão:</div>
        <div>Carne:</div>
        <div>Opcionais:</div>
        <div>Ações:</div>
      </div>
    </div>
    <div id="burger-table-rows">
      <div class="burger-table-row" v-for="burger in burgers" :key="burger.id">
        <div class="order-number">{{ burger.id }}</div>
        <div>{{ burger.nome }}</div>
        <div>{{ burger.pao }}</div>
        <div>{{ burger.carne }}</div>
        <div>
          <ul v-for="(opcional, index) in burger.opcionais" :key="index">
            <li>{{ opcional }}</li>
          </ul>
        </div>
        <div>
          <select name="status" class="status" @change="updateBurger($event, burger.id)">
            <option :value="s.tipo" v-for="s in status" :key="s.id" :selected="burger.status == s.tipo">
              {{ s.tipo }}
            </option>
          </select>
          <button class="delete-btn" @click="deleteBurger(burger.id)">Cancelar</button>
        </div>
      </div>
    </div>
  </div>
  <div v-else>
    <h2>Não há pedidos no momento!</h2>
  </div>
</template>
<script>
import api from '../../services/api';
var mixin = {
  methods: {
    getPedidos() {
      api.get('/burgers').then((res) => {
        this.burgers = res.data;
        this.getStatus()
      });
    },
    getStatus() {
      api.get('/status').then(
        (res) => {
          this.status = res.data
        }
      );
    },
    deleteBurger(id) {
      api.delete(`/burgers/${id}`).then(
        (res) => {
          this.getPedidos()
        }
      );
    },
    updateBurger(event, id) {
        const option = event.target.value;
        const data = { status: option };

        api.patch(`/burgers/${id}`, data).then(
          (res) => {
            console.log(res);
          }
        );
    }
  }
};
  export default {
    mixins: [mixin],
    name: "Dashboard",
    data() {
      return {
        burgers: null,
        burger_id: null,
        status: []
      }
    },
    methods: {

    },
    mounted () {
    this.getPedidos()
    }
  }
</script>

<style scoped>
  #burger-table {
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
    padding: 12px;
    border-bottom: 3px solid #333;
  }
  .burger-table-row {
    width: 100%;
    padding: 12px;
    border-bottom: 1px solid #CCC;
  }
  #burger-table-heading div,
  .burger-table-row div {
    width: 19%;
  }
  #burger-table-heading .order-id,
  .burger-table-row .order-number {
    width: 5%;
  }
  select {
    padding: 12px 6px;
    margin-right: 12px;
  }
  .delete-btn {
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
  
  .delete-btn:hover {
    background-color: transparent;
    color: #222;
  }
  
</style>