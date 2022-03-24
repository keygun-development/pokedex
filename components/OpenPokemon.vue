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
                    <div class="w-full flex justify-between" v-if="singlePokemonData.length > 0">
                        <div class="flex flex-col items-center c-pokemon__single-image">
                            <img
                                :src="'https://projectpokemon.org/images/normal-sprite/'+singlePokemonData[0].name+'.gif'"/>
                        </div>
                        <div class="flex flex-col items-center c-pokemon__single-stats ml-8">
                            <img
                                :src="'https://projectpokemon.org/images/normal-sprite/'+singlePokemonData[0].name+'.gif'"/>
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
            singlePokemonData: []
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
            fetch(this.pokemonUrl)
            .then(response => response.json())
            .then(data => {
                if (this.singlePokemonData.length > 0) {
                    while (this.singlePokemonData.length) {
                        this.singlePokemonData.pop();
                    }
                }
                this.singlePokemonData.push(data);
            })
        }
    }
}
</script>
