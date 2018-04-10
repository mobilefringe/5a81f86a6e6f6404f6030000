<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loader v-if="!dataLoaded"></loader>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak>
        		<div class="site_container">
        		    <div class="margin_60 hidden_phone"></div>
                    <div>
        		        <h3 class="home_page_title caps">Twitter Feed</h3>
        		    </div>
        		    <twitter-slider></twitter-slider>
        		    <div class="margin_60 hidden_phone"></div>
                    <div>
        		        <h3 class="home_page_title caps">Instagram Feed</h3>
        		    </div>
        		    <instagram></instagram>
                </div>
            </div>
        </transition>
    </div>
</template>

<script>
    define(["Vue", "vuex", "vue!twitter-slider", "vue!instagram"], function(Vue, Vuex, TwitterSlider, Instagram) {
        return Vue.component("social-component", {
            template: template, // the variable template will be injected
            data: function() {
                return {
                    dataLoaded: false,
                    
                }
            },
            created(){
                this.loadData().then(response => {
                   this.dataLoaded = true;
                });
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone'
                ]),
            },
            methods: {
                loadData: async function() {
                    try {
                        let results = await Promise.all([this.$store.dispatch("getData", "repos")]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                }
            }
        });
    });
</script>
