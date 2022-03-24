<template>
    <div>
        <Navigation></Navigation>
        <div class="container mt-4">
            <h1>
                Region:{{ selectedRegion }}
            </h1>
            <div class="w-full flex flex-wrap">
                <Querybuilder
                :region="selectedRegion"
                >

                </Querybuilder>
            </div>
        </div>
    </div>
</template>

<script>

import Navigation from '../components/Navigation'
import Querybuilder from "../components/Querybuilder";

export default {

    data() {
        return {
            selectedRegion: 'national'
        }
    },

    mounted() {
        window.addEventListener('hashchange', () => {
            const filter = document.location.href.split('#');
            this.selectedRegion = filter[1];
        });
    },

    components: {
        Querybuilder,
        Navigation
    }
}
</script>
