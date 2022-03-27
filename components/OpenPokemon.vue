<template>
    <div class="c-overlay">
        <div class="absolute flex items-center h-full">
            <div @click="prev()" class="c-pokemon__next-prev --prev" v-if="singlePokemonSpecies.length > 0 && singlePokemonData[0].id-1 !== 0">
                <img
                    :src="'https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/'+(singlePokemonData[0].id-1)+'.png'"/>
            </div>
        </div>
        <div class="absolute right-0 flex items-center h-full">
            <div @click="next()" class="c-pokemon__next-prev --next" v-if="singlePokemonSpecies.length > 0 && singlePokemonData[0].id+1 !== 899">
                <img
                    :src="'https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/'+(singlePokemonData[0].id+1)+'.png'"/>
            </div>
        </div>
        <div class="flex justify-center items-center h-full">
            <div class="w-8/12 flex justify-center">
                <div id="pokemon-single" class="c-pokemon__single-open" :class="changedType ? changedType : type">
                    <div class="flex justify-end">
                        <button @click="close()" class="c-pokemon__single-close">
                            X
                        </button>
                    </div>
                    <div class="w-full flex justify-between mt-4" v-if="singlePokemonSpecies.length > 0">
                        <div class="flex flex-col items-center justify-between c-pokemon__single-image text-center">
                            <img
                                :src="'https://projectpokemon.org/images/normal-sprite/'+singlePokemonData[0].name+'.gif'"
                                @error="fallBackSprite($event, singlePokemonData[0].species.url)"
                            />
                            <p>
                                {{ singlePokemonData[0].id }}. {{ singlePokemonData[0].name }}
                            </p>
                        </div>
                        <div class="flex flex-col c-pokemon__single-stats ml-8">
                            <div class="flex flex-wrap justify-between w-full">
                                <div class="c-pokemon__single-type w-5/12" :class="type.type.name" v-for="type in singlePokemonData[0].types">
                                    {{ type.type.name }}
                                </div>
                            </div>
                            <div class="mt-4">
                                <div v-for="ability in singlePokemonData[0].abilities" class="flex">
                                    <p>
                                        {{ ability.ability.name }}
                                    </p>
                                    <p class="ml-2" v-if="ability.is_hidden">
                                        (Hidden ability)
                                    </p>
                                </div>
                            </div>
                            <div class="mt-4 flex flex-wrap">
                                <div class="w-2/6" v-for="stat in singlePokemonData[0].stats">
                                    <div class="c-pokemon__single-stat m-2" :class="stat.stat.name">
                                        <p>
                                            {{ stat.stat.name }} {{ stat.base_stat }}
                                        </p>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div v-if="evolutionData.length > 0" class="mt-4 w-full c-pokemon__single-evolution">
                        <div class="flex justify-between">
                            <img
                                @click="getPokemonByEvolution(evolutionData[0].chain.species.url.split('/')[6])"
                                :src="'https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/'+evolutionData[0].chain.species.url.split('/')[6]+'.png'"/>
                            <img
                                v-if="evolutionData[0].chain.evolves_to[0]"
                                @click="getPokemonByEvolution(evolutionData[0].chain.evolves_to[0].species.url.split('/')[6])"
                                :src="'https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/'+evolutionData[0].chain.evolves_to[0].species.url.split('/')[6]+'.png'"/>
                            <img
                                v-if="evolutionData[0].chain.evolves_to[0] && evolutionData[0].chain.evolves_to[0].evolves_to[0]"
                                @click="getPokemonByEvolution(evolutionData[0].chain.evolves_to[0].evolves_to[0].species.url.split('/')[6])"
                                :src="'https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/'+evolutionData[0].chain.evolves_to[0].evolves_to[0].species.url.split('/')[6]+'.png'"/>
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
            singlePokemonSpecies: [],
            evolutionData: [],
            changedType: ""
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

        getPokemonByEvolution: function (id) {
            fetch("https://pokeapi.co/api/v2/pokemon/"+id)
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
            await fetch(speciesUrl)
            .then(response => response.json())
            .then(data => {
                if (this.singlePokemonSpecies.length > 0) {
                    while (this.singlePokemonSpecies.length) {
                        this.singlePokemonSpecies.pop();
                    }
                }
                this.singlePokemonSpecies.push(data);
            })
            await this.getEvolutionChain(this.singlePokemonSpecies[0].evolution_chain.url)
        },

        getEvolutionChain: async function (evolutionChain) {
            fetch(evolutionChain)
                .then(response => response.json())
                .then(data => {
                    if (this.evolutionData.length > 0) {
                        while (this.evolutionData.length) {
                            this.evolutionData.pop();
                        }
                    }
                    this.evolutionData.push(data);
                })
        },

        prev: function () {
            fetch("https://pokeapi.co/api/v2/pokemon/"+(this.singlePokemonData[0].id-1))
                .then(response => response.json())
                .then(data => {
                    if (this.singlePokemonData.length > 0) {
                        while (this.singlePokemonData.length) {
                            this.singlePokemonData.pop();
                        }
                    }
                    this.singlePokemonData.push(data);
                    this.getSinglePokemonSpecies(this.singlePokemonData[0].species.url)
                    this.setBackgroundByType();
                })
        },

        fallBackSprite: function(e, pokemonUrl) {
            e.target.src = 'https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/'+pokemonUrl.split('/')[6]+'.png'
        },

        next: function () {
            fetch("https://pokeapi.co/api/v2/pokemon/"+(this.singlePokemonData[0].id+1))
                .then(response => response.json())
                .then(data => {
                    if (this.singlePokemonData.length > 0) {
                        while (this.singlePokemonData.length) {
                            this.singlePokemonData.pop();
                        }
                    }
                    this.singlePokemonData.push(data);
                    this.getSinglePokemonSpecies(this.singlePokemonData[0].species.url)
                    this.setBackgroundByType();
                })
        },

        setBackgroundByType: function () {
            if (this.singlePokemonData[0].types.length > 1 && this.singlePokemonData[0].types[0] === 'Normal') {
                this.changedType = this.singlePokemonData[0].types[1].type.name
            } else {
                this.changedType = this.singlePokemonData[0].types[0].type.name
            }
        }
    }
}
</script>
