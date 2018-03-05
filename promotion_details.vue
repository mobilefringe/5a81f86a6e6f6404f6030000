<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loader v-if="!dataLoaded"></loader>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak>
        		<div class="site_container page_content">
        		    <div class="margin_60"></div>
        			<div id="promos_container">
    					<div class="promo_container">
    					    <div class="promo_img" v-if="locale=='en-ca'" v-lazy:background-image="currentPromo.image_url"></div>
    					    <div class="promo_img" v-else v-lazy:background-image="currentPromo.promo_image2_url_abs"></div>
    					    <div class="promo_content center">
    					        <p class="promo_title">{{ $t("promos_page.promotions") }}</p>
    					        <h3 class="margin_60" v-if="locale=='en-ca'">{{ currentPromo.name }}</h3>
    							<h3 class="margin_60" v-else>{{ currentPromo.name_2 }}</h3>
    							<p class="promo_desc">
    							    {{ currentPromo.start_date | moment("MMM D", timezone) }} - {{ currentPromo.end_date | moment("MMM D", timezone) }}
    							</p>
    					    </div>
    					    <div class="promo_details_desc">
            				    <div v-if="locale=='en-ca'" v-html="currentPromo.rich_description"></div>
            				    <div v-else v-html="currentPromo.rich_description_2"></div>
            				</div>
    					</div>
        			</div>
		        </div>
		    </div>
		</transition>
	</div>
</template>

<script>
    define(['Vue', 'vuex', 'moment', 'vue-lazy-load'], function(Vue, Vuex, moment, VueLazyload) {
        Vue.use(VueLazyload);
        return Vue.component("promo-details-component", {
            template: template, // the variable template will be injected,
            props:['id', 'locale'],
            data: function() {
                return {
                    dataLoaded: false,
                    currentPromo: null
                }
            },
            beforeRouteUpdate(to, from, next) {
                this.currentPromo = this.findPromoBySlug(to.params.id);
                    if (this.currentPromo === null || this.currentPromo === undefined){
                        this.$router.replace({ name: '404'});
                    }
                next();
                this.dataLoaded = true;
            },
            created(){
                this.loadData().then(response => {
                    this.updateCurrentPromo(this.id);
                    this.dataLoaded = true;
                });
            },
            watch: {
                currentPromo : function (){
                    if(this.currentPromo != null) {
                        console.log(this.currentPromo.store);
                        if (this.currentPromo.store != null && this.currentPromo.store != undefined) {
                            if(_.includes(this.currentPromo.store.image_url, 'missing')) {
                                this.currentPromo.store.image_url = "http://via.placeholder.com/400x400/757575";
                            }
                        } else if (this.currentPromo.store == null || this.currentPromo.store == undefined) {
                            this.currentPromo.store = {};
                            this.currentPromo.store.image_url =  "http://via.placeholder.com/400x400/757575";
                        }
                    }
                }
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'processedPromos',
                    'findPromoBySlug',
                    'findPromoById'
                ])
            },
            methods: {
                updateCurrentPromo (id) {
                    this.currentPromo = this.findPromoBySlug(id);
                    if (this.currentPromo === null || this.currentPromo === undefined){
                        this.$router.replace({ name: '404'});
                    }
                },
                loadData: async function() {
                    try {
                        // avoid making LOAD_META_DATA call for now as it will cause the entire Promise.all to fail since no meta data is set up.
                        let results = await Promise.all([this.$store.dispatch("getData", "promotions")]);
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                }
            }
        });
    });
</script>