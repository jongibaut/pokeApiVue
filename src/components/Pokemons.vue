<template>
    <h1 class="text-center text-white">{{title}}</h1>
    <div class="row">
      <div class="col-12">
          <input type="text" class= "form-control" placeholder="Ingrese el nombre del pokemon" v-on:change="getName">
        <div class="d-grid gap-2">
          <button v-on:click="filtrado" class="btn btn-info mt-4 mb-4">Buscar!</button>
        </div>
      </div>
    </div>
    
    <div class = "row justify-content-around">
      <div v-for = "pokemon in pokemons" :key = "pokemon.id " class = "col-4 mb-4">
        <div class="card" style="width: 18rem;">
          <img :src = "pokemon.image" class="card-img-top" alt="no se encontro la imagen">
          <div class="card-body">
            <h5 class="card-title">{{ pokemon.name }}</h5>
            <p class="card-text">id : {{pokemon.id}}</p>
            <div v-for = "tipos in pokemon.types" :key = "tipos.slot">
              <p class="card-text">tipo : {{tipos.type.name}}</p>
            </div>
          </div>
        </div>
  </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: 'Pokemons',
  data() {
    return{
      title: "Pokemons",
      pokemons: null,
      name: ''
    }
  },
   mounted() {
      this.get()
  },
  methods : {
    async get() {
      try {
      const {data} = await axios.get("https://pokeapi.co/api/v2/pokemon?limit=15"); //le pongo el limite de los primeros 15 pokemones
      const pokemones = data.results; //declaro una variable pokemones con los primeros 15 pokemones con su url
      const pokeData = await this.getPokeData(pokemones);
      this.pokemons = pokeData; //le doy el valor de los primeros 15 pokemones + sus datos(tipos, imagen, id, etc) con sus datos a pokemons
      }
      catch(e){ 
        console.error(e)
      }
    },
    async getPokeData(pokemones){
      //probe hacerlo con map y con conforEach en vez de usar un while y no funcionaba porque llamaba a una promesa dentro de otra promesa
      let i = 0;
      let pokeFinal = []
        while (i < pokemones.length) {
              const {data} = await axios.get(pokemones[i].url);
              //destructuro los datos que necesito de los pokemones asi no cargo el pokemon completo
              const {name, sprites, types, id} = data;
              const {front_default : image} = sprites //paso solo el sprite front_default que es el unico que necesito y le doy el nombre image asi se entiende que es la imagen
              const destructuredPoke = {
                name,
                image,
                types,
                id
              }
              pokeFinal.push(destructuredPoke); //array de objetos (pokeFinal) con los datos de los 15 pokemones  
          i++
        }
          return pokeFinal; //devuelvo los datos de cada pokemon apra acceder a las imagenes, tipos, ids, etc.
    },
    getName(e){
      this.name = e.target.value;
    },
    async filtrado(){
      const {data: pokes} = await axios.get("https://pokeapi.co/api/v2/pokemon?limit=15");
      const elPoke = pokes.results.filter(poke => poke.name === this.name);
      console.log(elPoke);
      const {data : pokeFiltrado} = await axios.get(elPoke[0].url); // traigo el pokemon por el url asi puedo buscar todas las veces que quiera, [0] porque me devuelve un array de un solo objeto
      //destructuro el pokemon como hice en getPokeData
      const {name, sprites, types, id} = pokeFiltrado;
      const {front_default : image} = sprites;
      const destructuredPoke = {
          name,
          image,
          types,
          id
      }
      const pokeFiltradoArray = [destructuredPoke]; //data me devuelve un objeto entonces lo convierto en un array de objetos
      this.pokemons = pokeFiltradoArray;
      /*
      const elPoke = this.pokemons.filter(poke => poke.name === this.name);
      this.pokemons = elPoke esto funciona pero no me deja seguir buscando puedo buscar una sola vez, ya que mi this.pokemons se convierte en un solo pokemon y la proxima vez que busque voy a buscar solamente ese pokemon y no todos.
      */
    }
  }
  
}
</script>


