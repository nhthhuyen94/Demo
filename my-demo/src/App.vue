<template>
    <v-app>
        <v-main>
        <!-- <HelloWorld/> -->
        <filter-demo @filter-return="checkFilter"/>
        </v-main>
    </v-app>
</template>

<script>
import FilterDemo from './components/FilterDemo.vue';
// import HelloWorld from './components/HelloWorld';

export default {
    name: 'App',

    components: {
        // HelloWorld,
        FilterDemo,
    },

    data: () => ({
        queryFilter: {},
    }),
    methods: {
        checkFilter(filter) {
            for (let key in filter) {
                if (filter[key].id) {
                    this.queryFilter[key] = filter[key].id
                } else {
                    delete this.queryFilter[key]
                }
            }
            this.fetchData()
        },
        fetchData() {
            console.log("filter's result")
        }
    }
};
</script>
