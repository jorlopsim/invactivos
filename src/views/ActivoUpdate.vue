<template>
    <div class="container">
        <h5>Activos Editar</h5>
    
        <div class="card">
            <div class="card-content">
                <form @submit.prevent="update()">
                    <p>Tipo activo: <input type="text" v-model="payload.type" required/></p>
                    <p>Marca: <input type="text" v-model="payload.brand" required/></p>
                    <p>Modelo: <input type="text" v-model="payload.model" required/></p>
                    <p>Estado:
                    <select name="Estado" id="estado-select" v-model="payload.state" required>
                        <option value="nuevo">Nuevo</option>
                        <option value="usado">Usado</option>
                        <option value="desuso">Desuso</option>
                    </select> 
                    </p>
                    <p v-if="areas.length > 0">Area:
                    <select v-model="payload.areaId" required>
                         <option :value="area.id" v-for="area in areas">{{ area.name }}</option>
                    </select>
                    </p>       
                    <button type="submit" class="waves-effect waves-light btn-small">Editar</button>
                    <button type="button" class="waves-effect waves-light btn-small" @click="salir()">Salir</button>
                </form>
            </div>
        </div>
    
    </div>
  
</template>

<script>
export default {
  name: 'AreaView',
  data() {
        const api = process.env.VUE_APP_API;
        return {
            api,
            items: [],
            areas: [],
            payload: {
                type: null,
                brand: null,
                model: null,
                state: null,
                areaId: null
            }
        }
    },
  methods: {    
    getOne() {
            this.axios({
                method: 'get',
                url: this.api + '/activos/' + this.$route.params.id
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
                    url: this.api + '/activos/' + this.$route.params.id,
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
    
        async getAreaList() {
            try{
              const { data }= await this.axios({
                method: 'get',
                url: this.api + '/areas',
              });
              this.areas=data;
            }
            catch(error) {
             console.log(error);
            }
        },

        getList(){ 
            this.axios({
                method: 'get',
                url: this.api + '/activos?_expand=area&q=' + this.search + this.toFilter
            }).
            then((response) => {
                this.items = response.data;
            }).
            catch((error) => {
            })

       },
        intSelect(){
            this.getList();
            this.getAreaList();
            var elems = document.querySelectorAll('select');
            var instances = M.FormSelect.init(elems);
        },
        salir()
        {
            window.history.back();
        }
  },
  components: {

},
async mounted() {
    this.getOne();
    await this.getAreaList();
    this.intSelect();
}
}
</script>
