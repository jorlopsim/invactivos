<template>
  <div class="container">
        <h5>Areas Editar</h5>
    
        <div class="card">
            <div class="card-content">
                <form @submit.prevent="update()">
                    <p>Nombre Area: <input type="text" v-model="payload.name" required/></p>
                    <p>Nombre Encargado: <input type="text" v-model="payload.enc_name" required/></p>
                    <p>Nro Funcionarios: <input type="text" v-model="payload.nro_official" required/></p>
                    <button type="submit" class="waves-effect waves-light btn-small">Editar</button>
                    <button type="button" class="waves-effect waves-light btn-small" @click="salir()">Salir</button>
                </form>
            </div>
        </div>
    
    </div>
</template>

<script>
import Area from './Area.vue';

export default {
  name: 'AreaView',
  data() {
        const api = process.env.VUE_APP_API;
        return {
            api,
            items: [],
            payload: {
                name: null,
                enc_name:null,
                nro_official:null,
            }
        }
    },
  methods: {    
    getOne() {
            this.axios({
                method: 'get',
                url: this.api + '/areas/' + this.$route.params.id  
            }).
            then((response) => {
                this.payload = response.data;
            }).
            catch((error) => {
                console.log(error);
            });
        },
        update() {
            if (confirm("Esta seguro de editar?.")) {
                this.axios({
                    method: 'patch',
                    url: this.api + '/areas/' + this.$route.params.id,
                    data: this.payload
                }).
                then((response) => {
                    console.log(response);
                }).
                catch((error) => {
                    console.log(error);
                });
            }
        },
        salir()
        {
            window.history.back();
        }
  },
  components: {

},
mounted() {
    this.getOne();
}
}
</script>
