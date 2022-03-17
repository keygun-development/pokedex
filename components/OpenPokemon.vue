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
                    <div class="w-full flex">
                        <div class="w-6/12 bg-white rounded-2xl">
                            {{  }}
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>

export default {

    props: {
        pokemonName: String,
        type: String,
    },

    watch: {
        pokemonName: function(pokemonName){
            this.pokemonName = pokemonName
        },
    },

    methods: {
        close () {
            this.$emit('close')
        }
    }
}
</script>
