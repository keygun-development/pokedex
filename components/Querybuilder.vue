<template>
    <div class="w-full flex flex-wrap">
        <div
            class="w-1/5 my-8 flex"
            v-for="(pokemon, index) in pokemonEntries.slice(pokemonOffset, pokemonPerPage)">
            <div
                @click="setOpenPokemon(pokemon.pokemon_species.name, type[index])"
                class="c-pokemon__single flex flex-col items-center"
                :class="type[index]">
                <img
                    :src="'https://projectpokemon.org/images/normal-sprite/'+pokemon.pokemon_species.name+'.gif'"
                    @error="fallBackSprite($event, pokemon.pokemon_species.url)" />
                {{ pokemon.pokemon_species.name }}
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
        :pokemon-name="clickedPokemon"
        :type="clickedPokemonType"
        @close="closePokemon()"
        >

        </open-pokemon>
    </div>
</template>

<script>

import OpenPokemon from "./OpenPokemon";
import pokemonData from "../pokemonData"

export default {
    components: {OpenPokemon},
    data() {
        return {
            baseUrl: 'https://pokeapi.co/api/v2/',
            pokemonResults: [],
            pokemonEntries: [],
            pokemonPerPage: 20,
            pokemonOffset: 0,
            currentPage: 1,
            maxPage: 0,
            open: false,
            clickedPokemon: '',
            type: [],
            clickedPokemonType: '',
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
                })
            this.maxPage = Math.ceil(this.pokemonEntries.length / 20);
            this.getPokemonType();
        },

        previousPage: function () {
            if(this.pokemonOffset !== 0) {
                this.pokemonOffset = this.pokemonOffset - 20;
                this.pokemonPerPage = this.pokemonPerPage - 20;
                this.currentPage -= 1;
                this.getPokemonType();
            }
        },

        nextPage: function () {
            if((this.pokemonOffset + 20) < this.pokemonEntries.length) {
                this.pokemonOffset = this.pokemonOffset + 20;
                this.pokemonPerPage = this.pokemonPerPage + 20;
                this.currentPage += 1;
                this.getPokemonType();
            }
        },

        getPokemonType: function () {
            if (this.type.length > 0) {
                while (this.type.length) {
                    this.type.pop();
                }
            }

            for (let i = this.pokemonOffset; i<this.pokemonPerPage;i++) {
                if(this.pokemonEntries[i].pokemon_species.name === pokemonData[i].name.english.toLowerCase()) {
                    this.type.push(pokemonData[i].type[0])
                } else {
                    this.type.push("Normal")
                }
            }
        },

        fallBackSprite: function(e, pokemonUrl) {
            e.target.src = 'https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/'+this.getIdByUrl(pokemonUrl)+'.png'
        },

        getIdByUrl: function (pokemonUrl) {
            const arr = pokemonUrl.split('/')
            return arr[arr.length - 2];
        },

        closePokemon: function () {
            self = this;
            document.getElementById('pokemon-single').classList.add('close')
            setTimeout(function(){ self.open = false }, 3000);
        },

        setOpenPokemon: function (pokemonName, type) {
            this.open = true;
            this.clickedPokemon = pokemonName;
            this.clickedPokemonType = type
        }
    }
}

</script>
