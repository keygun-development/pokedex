<template>
    <div class="c-overlay">
        <div class="flex justify-center items-center h-full">
            <div class="w-8/12 flex justify-center">
                <div id="pokemon-single" class="c-pokemon__single-open" :class="type">
                    <div class="flex justify-end">
                        <button @click="close()" class="c-pokemon__single-close">
                            X
                        </button>
                    </div>
                    <div class="w-full flex justify-between mt-4" v-if="singlePokemonSpecies.length > 0">
                        <div class="flex flex-col items-center c-pokemon__single-image">
                            <img
                                :src="'https://projectpokemon.org/images/normal-sprite/'+singlePokemonData[0].name+'.gif'"/>
                        </div>
                        <div class="flex flex-col c-pokemon__single-stats ml-8">
                            <div class="flex flex-wrap justify-between w-full">
                                <div class="c-pokemon__single-type w-5/12" :class="type.type.name" v-for="type in singlePokemonData[0].types">
                                    {{ type.type.name }}
                                </div>
                            </div>
                            <div class="mt-4">
                                <p>
                                    Base happiness: {{ singlePokemonSpecies[0].base_happiness }}
                                </p>
                                <p>
                                    Capture rate: {{ singlePokemonSpecies[0].capture_rate }}
                                </p>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>

export default {

    data() {
        return {
            singlePokemonData: [],
            singlePokemonSpecies: []
        }
    },

    props: {
        pokemonUrl: String,
        type: String,
    },

    watch: {
        pokemonUrl: function(pokemonUrl){
            this.pokemonUrl = pokemonUrl;
            this.getSinglePokemon();
            this.getSinglePokemonSpecies();
        },
    },

    mounted() {
        this.getSinglePokemon();
    },

    methods: {
        close () {
            this.$emit('close')
        },

        getSinglePokemon: function () {
            fetch("https://pokeapi.co/api/v2/pokemon/"+this.pokemonUrl.split('/')[6])
            .then(response => response.json())
            .then(data => {
                if (this.singlePokemonData.length > 0) {
                    while (this.singlePokemonData.length) {
                        this.singlePokemonData.pop();
                    }
                }
                this.singlePokemonData.push(data);
                this.getSinglePokemonSpecies(this.singlePokemonData[0].species.url)
            })
        },

        getSinglePokemonSpecies: async function (speciesUrl) {
            fetch(speciesUrl)
            .then(response => response.json())
            .then(data => {
                if (this.singlePokemonSpecies.length > 0) {
                    while (this.singlePokemonSpecies.length) {
                        this.singlePokemonSpecies.pop();
                    }
                }
                this.singlePokemonSpecies.push(data);
            })
        }
    }
}
</script>
