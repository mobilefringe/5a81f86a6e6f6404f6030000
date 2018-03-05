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
    					    <div class="promo_content">
    					        <p class="promo_title">{{ $t("promos_page.promotions") }}</p>
    					        <h3 class="" v-if="locale=='en-ca'">{{ currentPromo.name }}</h3>
    							<h3 class="" v-else>{{ currentPromo.name }}</h3>
    					    </div>
    					    <div class="promo_desc">
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
                    currentPromo: null,
                    // storePromos : null,
                    // storeHours : null,
                    // promoBanner : null
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
                        if (this.currentPromo.store != null && this.currentPromo.store != undefined && _.includes(this.currentPromo.store.image_url, 'missing')) {
                            this.currentPromo.store.image_url = "http://via.placeholder.com/400x400/757575";
                        }
                        else if (this.currentPromo.store == null || this.currentPromo.store == undefined) {
                            this.currentPromo.store = {};
                            this.currentPromo.store.image_url =  "http://via.placeholder.com/400x400/757575";
                        }
                        var vm = this;
                        var temp_promo = [];
                        var current_id =_.toNumber(this.currentPromo.id);
                        _.forEach(this.currentPromo.store.promotions, function(value, key) {
                            if(_.toNumber(value) != current_id){
                                var current_promo = vm.findPromoById(value);
                                current_promo.description_short = _.truncate(current_promo.description, {'length': 70});
                                temp_promo.push(current_promo);
                            }
                        });
                        this.storePromos = temp_promo;
                    }
                    if(this.currentPromo.store) {
                        var storeHours = [];
                        var vm = this;
                        _.forEach(this.currentPromo.store.store_hours, function (value, key) {
                            var hour = vm.findHourById(value);
                            if(hour.day_of_week === 0){
                                hour.order = 7;
                            }
                            else {
                                hour.order = hour.day_of_week;
                            }
                            storeHours.push();
                        });
                        this.storeHours = _.sortBy(storeHours, [function(o) { return o.order; }]);
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
                ]),
                allPromos() {
                    return this.processedPromos;
                },
            },
            methods: {
                updateCurrentPromo (id) {
                    this.currentPromo = this.findPromoBySlug(id);
                    if (this.currentPromo === null || this.currentPromo === undefined){
                        this.$router.replace({ name: '404'});
                    }
                    console.log(this.currentPromo)
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