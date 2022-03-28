<template>
    <div class="bg-red-500 py-4 lg:p-12 c-navigation">
        <div class="container flex items-center">
            <a class="c-navigation__logo mb-4 md:mb-0" href="#national">
                <img src="../assets/images/pokedex.png" />
            </a>
            <div class="c-navigation__items w-full justify-between ml-8 hidden md:flex">
                <a class="text-white" href="#kanto">
                    Kanto
                </a>
                <a class="text-white" href="#original-johto">
                    Johto
                </a>
                <a class="text-white" href="#hoenn">
                    Hoenn
                </a>
                <a class="text-white" href="#original-sinnoh">
                    Sinnoh
                </a>
                <a class="text-white" href="#original-unova">
                    Unova
                </a>
                <a class="text-white" href="#kalos-central">
                    Kalos
                </a>
                <a class="text-white" href="#original-alola">
                    Alola
                </a>
                <a class="text-white" href="#galar">
                    Galar
                </a>
            </div>
            <div class="md:hidden block w-full">
                <div class="flex justify-end">
                    <div class="text-right ml-auto h-full">
                        <div class="w-full flex justify-end -mt-4 h-full">
                            <div :class="{'is-active':showNav}" class="hamburger hamburger--emphatic" type="button">
                                <span class="hamburger-box">
                                    <span @click="showNav = !showNav" class="hamburger-inner"></span>
                                </span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div :class="{'opennav': showNav}" class="c-navigation__collapse flex flex-col">
            <a @click="showNav = !showNav" class="text-white" href="#kanto">
                Kanto
            </a>
            <a @click="showNav = !showNav" class="text-white" href="#original-johto">
                Johto
            </a>
            <a @click="showNav = !showNav" class="text-white" href="#hoenn">
                Hoenn
            </a>
            <a @click="showNav = !showNav" class="text-white" href="#original-sinnoh">
                Sinnoh
            </a>
            <a @click="showNav = !showNav" class="text-white" href="#original-unova">
                Unova
            </a>
            <a @click="showNav = !showNav" class="text-white" href="#kalos-central">
                Kalos
            </a>
            <a @click="showNav = !showNav" class="text-white" href="#original-alola">
                Alola
            </a>
            <a @click="showNav = !showNav" class="text-white" href="#galar">
                Galar
            </a>
        </div>
    </div>
</template>

<script>

export default {
    data() {
        return {
            showNav: false
        }
    }
}
</script>
