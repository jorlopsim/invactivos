<template>
    <div class="container">
        <h5>Activos</h5>
    
        <div class="card">
            <div class="card-content">
                <form @submit.prevent="nuevo()">
                    <h5>Nuevo activo</h5>
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
                            <option :value="area.id" v-for="area in areas">{{area.name}}</option>
                        </select>
                    </p>       
                    <button type="submit" class="waves-effect waves-light btn-small">Agregar</button>
                </form>
            </div>
        </div>
    
        <div class="card">
            <div class="card-content">
                <form @submit.prevent="getList()">
                    <h5>Buscar Activo</h5>
                    <p>Tipo Activo: <input type="search" v-model="search" @search="getList()" /></p>
                    <button type="submit" class="waves-effect waves-light btn-small">buscar</button>
                </form>
            </div>
        </div>
    
        <div class="card">
            <div class="card-content">
                <h5>filtros</h5>
                <div class="input-field ">
                    <select @change="filter('state',$event.target.value)">
                        <option value="" selected>todos</option>
                        <option value="nuevo">Nuevo</option>
                        <option value="usado">Usado</option>
                        <option value="desuso">Desuso</option>
                    </select>
                    <label>Materialize Select</label>
                </div>
    
            </div>
        </div>
    
        <div class="card">
            <div class="card-content">
                <table>
                    <thead>
                     <tr>
                        <th>Id</th>
                        <th>Tipo</th>
                        <th>Marca</th>
                        <th>Modelo</th>
                        <th>Estado</th>
                        <th>Area</th>
                    </tr>
                    </thead>
    
                    <tbody>
                        <tr v-for="item in items">
                            <td>{{item.id}}</td>
                            <td>{{item.type}}</td>
                            <td>{{item.brand}}</td>
                            <td>{{item.model}}</td>
                            <td>{{item.state}}</td>
                            <td>{{item.area.name}}</td>
                            <td>
                                <a class="app-btn btn-small btn-floating btn-large waves-effect waves-light red"><i class="material-icons" @click="eliminar(item.id)" >delete</i></a>
                                <a class="app-btn btn-small btn-floating btn-large waves-effect waves-light blue "><i class="material-icons" @click="update(item.id)" >edit</i></a>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</template>

<script>
export default {
  name: 'ActivoView',
  data() {
        const api = process.env.VUE_APP_API;
        return {
            api,
            items: [],
            search: '',
            toFilter: '',
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
    nuevo() {
            this.axios({
                method: 'post',
                url: this.api + '/activos',
                data: this.payload
            }).
            then((response) => {
                this.getList();
                console.log(response);
            }).
            catch((error) => {
                console.log(error);
            });
        },
    update(id) {
            this.$router.push('/activo/' + id);
        },
       
     eliminar(id) {
            if (confirm("Esta seguro de eliminar?.")) {
                this.axios({
                    method: 'delete',
                    url: this.api + '/activos/' + id
                }).
                then((response) => {
                    this.getList();
                }).
                catch((error) => {
                    console.log(error);
                });
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
        
        filter(name, value) {
            this.toFilter = value === '' ? '' : '&' + name + '=' + value;
            this.getList();
        },

        intSelect(){
            this.getList();
            this.getAreaList();
            const elems = document.querySelectorAll('select');
            const instances = M.FormSelect.init(elems);
        }
    
    },    
  components: {

},
    async mounted() {
        this.getList();
        await this.getAreaList();
        this.intSelect();
    }
}
</script>
