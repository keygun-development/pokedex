<template>
    <div class="w-full flex flex-wrap">
        <div
            class="w-1/5 my-8 flex"
            v-for="(pokemon, index) in pokemonEntries.slice(pokemonOffset, pokemonPerPage)">
            <div
                @click="setOpenPokemon(pokemon.pokemon_species.url, type[index])"
                class="c-pokemon__single flex flex-col items-center"
                :class="type[index]">
                <img
                    :src="'https://projectpokemon.org/images/normal-sprite/'+pokemon.pokemon_species.name+'.gif'"
                    @error="fallBackSprite($event, pokemon.pokemon_species.url); type.push('Normal')" />
                <p>
                    {{ pokemon.pokemon_species.url.split('/')[6] }}.
                </p>
                <p>
                    {{ pokemon.pokemon_species.name }}
                </p>
            </div>
        </div>
        <div class="flex justify-between w-full">
            <a @click="previousPage()">Vorige</a>
            <p>
                {{ currentPage }}/{{ maxPage }}
            </p>
            <a @click="nextPage()">Volgende</a>
        </div>
        <open-pokemon
        v-if="open"
        :pokemon-url="clickedPokemon"
        :type="clickedPokemonType"
        @close="closePokemon()"
        >

        </open-pokemon>
    </div>
</template>

<script>

import OpenPokemon from "./OpenPokemon";

export default {
    components: {OpenPokemon},
    data() {
        return {
            baseUrl: 'https://pokeapi.co/api/v2/',
            pokemonData: [],
            pokemonEntries: [],
            pokemonPerPage: 20,
            pokemonOffset: 0,
            currentPage: 1,
            maxPage: 0,
            open: false,
            clickedPokemon: '',
            type: [],
            clickedPokemonType: '',
            pokemonStartingId: 0,
            pokemonEndingId: 20
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
            this.resetPages()
            this.getPokemon()
        },
    },

    methods: {
        getPokemon: async function () {
            await fetch(this.baseUrl+'pokedex/'+this.region)
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

                    this.getStartingId(parseInt(data.pokemon_entries[0].pokemon_species.url.split('/')[6]));
                })
            await fetch('https://raw.githubusercontent.com/Purukitto/pokemon-data.json/master/pokedex.json')
                .then(response => response.json())
                .then(data => {
                    this.pokemonData.push(data);
                })
            this.maxPage = Math.ceil(this.pokemonEntries.length / 20);
            await this.getPokemonType();
        },

        previousPage: function () {
            if(this.pokemonOffset !== 0) {
                this.pokemonOffset -= 20;
                this.pokemonPerPage -= 20;
                this.currentPage -= 1;
                this.pokemonStartingId -= 20
                this.pokemonEndingId -= 20
                this.getPokemonType();
            }
        },

        nextPage: function () {
            if((this.pokemonOffset + 20) < this.pokemonEntries.length) {
                this.pokemonOffset += 20;
                this.pokemonPerPage += 20;
                this.currentPage += 1;
                this.pokemonStartingId += 20
                this.pokemonEndingId += 20
                this.getPokemonType();
            }
        },

        resetPages: function () {
            this.pokemonOffset = 0;
            this.pokemonPerPage = 20;
            this.currentPage = 1;
            this.pokemonStartingId = 0
            this.pokemonEndingId = 0
        },

        getPokemonType: async function () {
            if (this.type.length > 0) {
                while (this.type.length) {
                    this.type.pop();
                }
            }
            for (let i = this.pokemonOffset; i<this.pokemonPerPage;i++) {
                if (this.region === 'national') {
                    if (this.pokemonEntries[i].pokemon_species.name === this.pokemonData[0][i].name.english.toLowerCase()) {
                        if (this.pokemonData[0][i].type.length > 1 && this.pokemonData[0][i].type[0] === 'Normal') {
                            this.type.push(this.pokemonData[0][i].type[1])
                        } else {
                            this.type.push(this.pokemonData[0][i].type[0])
                        }
                    } else {
                        this.type.push("Normal")
                    }
                } else {
                    this.getTypeByName(this.pokemonEntries[i].pokemon_species.name);
                }
            }
            if (this.type.length < 20) {
                while(this.type.length < 20) {
                    this.type.push("Normal")
                }
            }
        },

        fallBackSprite: function(e, pokemonUrl) {
            e.target.src = 'https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/'+pokemonUrl.split('/')[6]+'.png'
        },

        closePokemon: function () {
            self = this;
            document.getElementById('pokemon-single').classList.add('close')
            setTimeout(function(){ self.open = false }, 2000);
        },

        setOpenPokemon: function (pokemonUrl, type) {
            this.open = true;
            this.clickedPokemon = pokemonUrl;
            this.clickedPokemonType = type
        },

        getStartingId: function (id) {
            this.pokemonStartingId = id
            this.pokemonEndingId = (id + 20)
        },

        getTypeByName: function (name) {
            for (let i = 0; i<this.pokemonData[0].length;i++) {
                if (this.pokemonData[0][i].name.english.toLowerCase() === name) {
                    if (this.pokemonData[0][i].type.length > 1 && this.pokemonData[0][i].type[0] === 'Normal') {
                        this.type.push(this.pokemonData[0][i].type[1])
                    } else {
                        this.type.push(this.pokemonData[0][i].type[0])
                    }
                }
            }
        }
    }
}

</script>
