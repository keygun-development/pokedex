<template>
    <div class="w-full flex flex-wrap">
        <div class="w-1/5 c-pokemon__single mb-8 flex flex-col items-center" v-for="pokemon in pokemonEntries.slice(pokemonOffset, pokemonPerPage)">
            <img :src="'https://projectpokemon.org/images/normal-sprite/'+pokemon.pokemon_species.name+'.gif'" />
            {{ pokemon.pokemon_species.name }}
        </div>
        <div class="flex justify-between w-full">
            <a @click="previousPage()">Vorige</a>
            <a @click="nextPage()">Volgende</a>
        </div>
    </div>
</template>

<script>

export default {

    data() {
        return {
            baseUrl: 'https://pokeapi.co/api/v2/',
            pokemonResults: [],
            pokemonEntries: [],
            pokemonPerPage: 20,
            pokemonOffset: 0
        }
    },

    props: {
        region: {
            type: String,
            default: 'national'
        }
    },

    mounted() {
        this.getPokemon();
    },

    watch: {
        region: function(region){
            this.region = region
            this.getPokemon()
        },
    },

    methods: {
        getPokemon: async function () {
            await fetch(this.baseUrl+'pokedex/'+this.region+'?limit=20&offset=0')
                .then(response => response.json())
                .then(data => {
                    if (this.pokemonEntries.length > 0) {
                        while (this.pokemonEntries.length) {
                            this.pokemonEntries.pop();
                        }
                    }
                    for (let i = 0; i<data.pokemon_entries.length; i++) {
                        this.pokemonEntries.push(data.pokemon_entries[i]);
                    }
                })
        },

        previousPage: function () {
            if(this.pokemonOffset !== 0) {
                this.pokemonOffset = this.pokemonOffset - 20;
                this.pokemonPerPage = this.pokemonPerPage - 20;
            }
        },

        nextPage: function () {
            if((this.pokemonOffset + 20) < this.pokemonEntries.length) {
                this.pokemonOffset = this.pokemonOffset + 20;
                this.pokemonPerPage = this.pokemonPerPage + 20;
            }
        }
    }

}

</script>
