<template>
  <v-app>
    <v-container>
      <v-text-field
        v-model="search"
        label="Pesquisar"
        placeholder="Charmander"
        solo
      >
      </v-text-field>
      <v-row>
        <v-col
          cols="2"
          v-for="pokemon in filtered_pokemons"
          :key="pokemon.name"
        >
          <v-card @click="get_data(pokemon)">
            <v-container>
              <v-row class="mx-0 d-flex justify-center">
                <img
                  :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${get_id(
                    pokemon
                  )}.png`"
                  :alt="pokemon.name"
                  width="80%"
                />
              </v-row>
              <h2 class="text-center">{{ get_name(pokemon) }}</h2>
            </v-container>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
    <v-dialog v-model="show_dialog" width="60%">
      <v-card v-if="selected_pokemon" class="px-4">
        <v-container>
          <v-row class="d-flex align-center">
            <v-col cols="4">
              <img
                :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${selected_pokemon.id}.png`"
                :alt="selected_pokemon.name"
                width="100%"
              />
            </v-col>
            <v-col cols="8">
              <h1 class="ml-2 my-2">
                {{ get_name(selected_pokemon) }}
              </h1>
              <v-chip
                label
                class="ml-2 my-2" color="rgb(155,224,158)"
                v-for="type in selected_pokemon.types"
                :key="type.slot"
                >{{
                  type.type.name.charAt(0).toUpperCase() +
                  type.type.name.slice(1)
                }}</v-chip
              >
              <v-divider class="my-3"></v-divider>
              <h2 class="ml-2 my-2">Tamanho</h2>
              <v-chip label class="ml-2 my-2" color="rgb(155,224,158)"
                ><span
                  >Altura - {{ selected_pokemon.height * 2.54 }} cm</span
                ></v-chip
              >
              <v-chip label class="ml-2 my-2" color="rgb(155,224,158)"
                ><span
                  >Peso -
                  {{ (selected_pokemon.weight * 0.45359237).toFixed(0) }}
                  kgs</span
                ></v-chip
              >

              <v-divider class="my-3"></v-divider>
              <h2 class="ml-2 my-2">Habilidades</h2>
              <v-chip label class="ml-2 my-2" color="rgb(155,224,158)"
                ><span
                  >HP - 
                  {{ selected_pokemon.stats[0].base_stat }}
                </span></v-chip
              >

              <v-chip label class="ml-2 my-2" color="rgb(155,224,158)"
                ><span
                  >Ataque - 
                  {{ selected_pokemon.stats[1].base_stat }}
                </span></v-chip
              >

              <v-chip label class="ml-2 my-2" color="rgb(155,224,158)"
                ><span
                  >Defesa - 
                  {{ selected_pokemon.stats[2].base_stat }}
                </span></v-chip
              >

              <v-chip label class="ml-2 my-2" color="rgb(155,224,158)"
                ><span
                  >Ataque Especial - 
                  {{ selected_pokemon.stats[3].base_stat }}
                </span></v-chip
              >

              <v-chip label class="ml-2 my-2" color="rgb(155,224,158)"
                ><span
                  >Defesa Especial - 
                  {{ selected_pokemon.stats[4].base_stat }}
                </span></v-chip
              >

              <v-chip label class="ml-2 my-2" color="rgb(155,224,158)"
                ><span
                  >Velocidade - 
                  {{ selected_pokemon.stats[5].base_stat }}
                </span>
              </v-chip>

              <v-divider class="my-3"></v-divider>
              <h2 class="ml-2 my-2">Evoluções</h2>

              <v-chip label class="ml-2 my-2" color="rgb(155,224,158)"
                ><span
                  >1 - 
                  {{ evolution1 }}
                </span>
              </v-chip>

              <v-chip label class="ml-2 my-2" color="rgb(155,224,158)"
                ><span
                  >2 - 
                  {{ evolution2 }}
                </span>
              </v-chip>

              <v-chip label class="ml-2 my-2" color="rgb(155,224,158)"
                ><span
                  >3 - 
                  {{ evolution3 }}
                </span>
              </v-chip>
            </v-col>
          </v-row>
        </v-container>
      </v-card>
    </v-dialog>
  </v-app>
</template>


<script>
import axios from "axios";
export default {
  name: "App",

  components: {},

  data: () => ({
    pokemons: [],
    search: "",
    show_dialog: false,
    selected_pokemon: null,
    evolution1: "",
    evolution2: "",
    evolution3: ""
  }),

  mounted() {
    axios
      .get("https://pokeapi.co/api/v2/pokemon?limit=984")
      .then((response) => {
        this.pokemons = response.data.results;
      });
  },

  methods: {
    get_id(pokemon) {
      return Number(pokemon.url.split("/")[6]);
    },
    get_name(pokemon) {
      return pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1);
    },
    async show_pokemon(id) {
      await axios
        .get(`https://pokeapi.co/api/v2/pokemon/${id}`)
        .then((response) => {
          this.selected_pokemon = response.data;
        });
    },
    async show_evolution(id) {
      await axios
        .get(`https://pokeapi.co/api/v2/pokemon-species/${id}`)
        .then((response) => {
          this.evolution = response.data.evolution_chain.url;
        });
    },

    async get_data(pokemon) {
      await this.show_pokemon(this.get_id(pokemon)),
        await this.show_evolution(this.get_id(pokemon)),
        await this.show_all_evolutions();
      this.show_dialog = !this.show_dialog;
    },

    async show_all_evolutions() {
      await axios.get(`${this.evolution}`).then((response) => {
        this.evolution3 = response.data.chain.evolves_to[0].evolves_to[0].species.name.charAt(0).toUpperCase() + response.data.chain.evolves_to[0].evolves_to[0].species.name.slice(1);
        this.evolution2 = response.data.chain.evolves_to[0].species.name.charAt(0).toUpperCase() + response.data.chain.evolves_to[0].species.name.slice(1);
        this.evolution1 = response.data.chain.species.name.charAt(0).toUpperCase() + response.data.chain.species.name.slice(1);
      });
    },
  },

  computed: {
    filtered_pokemons() {
      return this.pokemons.filter((item) => {
        return item.name.includes(this.search);
      });
    },
  },
};
</script>

<style>
#app {
  background: linear-gradient(
      to bottom right,
      rgba(10, 10, 10, 1),
      rgb(155,224,158)
    )
    no-repeat center center fixed !important;
  -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
  background-size: cover !important;
  background-position: center;
  min-height: 100vh;
}
</style>
