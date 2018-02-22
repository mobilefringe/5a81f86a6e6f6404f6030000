<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loader v-if="!dataLoaded"></loader>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak class="site_container">
                <div class="">
                    <div class="details_store_name">
                        <h3>{{ currentStore.name}}</h3>
                        <p>{{ storeCategory }}</p>
                    </div>
    
					<div class="details_store_image">
						<img src="currentStore.image_url" class="image"/>
					</div>
    				<div class="details_store_desc">
    				    <div v-html="currentStore.description"></div>
    				    <a :href="'//'+currentStore.website" target="_blank"><div>{{$t("stores_page.store_website")}}</div></a>
    				</div>
    			</div>
		    </div>
		</transition>
	</div>
</template>

<!--<hr class="green_hr visible_phone">-->
    				<!--<div class="col-sm-8 text-left">-->
    				<!--	<h4 v-if="currentStore.rich_description" class="store_dets_title caps"> {{$t("stores_page.about_us")}}</h4>-->
    				<!--	<div class="text-left promo_description">-->
    				<!--		<p v-if="locale=='en-ca'" v-html="currentStore.rich_description"></p>-->
    				<!--		<p v-else v-html="currentStore.rich_description_2"></p>-->
    				<!--	</div>-->
    				<!--	<div class="store_promo_container" v-if="promotions.length > 0">-->
    				<!--		<div class="promo_container_title text-left caps"></div>-->
    				<!--		<h4 v-if="currentStore.rich_description" class="store_dets_title caps margin_30">{{$t("promos_page.promotions")}}</h4>-->
    				<!--		<div class="row store_promo_dets text-left" v-for="promo in promotions">-->
    				<!--			<div class="col-sm-6 no_padding" >-->
    				<!--				<div class="promo_div_image">-->
    				<!--					<img v-lazy="promo.image_url" class="image" alt=""/>-->
    				<!--				</div>-->
    				<!--				<div class="store_promo_dets_container padding_tb_20">-->
    				<!--				    <p class="promo_div_name" v-if="locale=='en-ca'">{{promo.name}}</p>-->
    				<!--				    <p class="promo_div_name" v-else>{{promo.name_2}}</p>-->
        <!--								<p class="promo_div_date"><i class="fa fa-calendar"></i>{{promo.start_date | moment("MMM D", timezone)}} - {{promo.end_date | moment("MMM D", timezone)}}</p>-->
        <!--								<div class="text-center">-->
        <!--								    <span class="store_dets_btn">-->
        <!--    									<router-link :to="'/promotions/'+promo.slug" class="" >{{$t("promos_page.read_more")}}</router-link>-->
        <!--    								</span>-->
        <!--								</div>-->
        								
    				<!--				</div>-->
    				<!--			</div>-->
    				<!--		</div>-->
    				<!--	</div>-->
    				<!--</div>-->

<script>
    define(['Vue', 'vuex', 'moment', 'vue-lazy-load'], function(Vue, Vuex, moment, VueLazyload) {
        Vue.use(VueLazyload);
        return Vue.component("store-details-component", {
            template: template, // the variable template will be injected,
            data: function() {
                return {
                    dataLoaded: false,
                    currentStore: null,
                    promotions : [],
                    jobs:[],
                    
                    pageBanner : null ,
                    storeHours :[]
                }
            },
            props:['id', 'locale'],
            beforeRouteUpdate(to, from, next) {
                this.currentStore = this.findStoreBySlug(to.params.id);
                if (this.currentStore === null || this.currentStore === undefined){
                    this.$router.replace({ name: '404'});
                }
                next();
            },
            created (){
                this.loadData().then(response => {
                    this.dataLoaded = true;
                    this.updateCurrentStore(this.id);
                    // var temp_repo = this.findRepoByName('Stores Banner');
                    // if(temp_repo) {
                    //     this.pageBanner = temp_repo.images[0];
                    // }
                    // this.pageBanner = this.pageBanner;
                });
                 console.log("locale created", this.locale);
            },
            watch: {
                currentStore: function() {
                    console.log(this.currentStore)
                    if ( _.includes(this.currentStore.store_front_url_abs, 'missing')) {
                        this.currentStore.store_front_url_abs = "//codecloud.cdn.speedyrails.net/sites/5a81f86a6e6f6404f6030000/image/png/1516652189884/ES_logo_red2.png";
                    }
                    // var vm = this;
                    // var temp_promo = [];
                    // var temp_job = [];
                    // _.forEach(this.currentStore.promotions, function(value, key) {
                    //     var current_promo = vm.findPromoById(value);
                    //     current_promo.description_short = _.truncate(current_promo.description, {
                    //         'length': 70
                    //     });
                    //     temp_promo.push(current_promo);
                    // });
                    // _.forEach(this.currentStore.jobs, function(value, key) {
                    //     var current_job = vm.findJobById(value);
                    //     current_job.description_short = _.truncate(current_job.description, {
                    //         'length': 70
                    //     });
                    //     temp_job.push(current_job);

                    // })
                    // this.promotions = temp_promo;
                    // this.jobs = temp_job;
                    
                    // var storeHours = [];
                    // var vm = this;
                    // _.forEach(this.currentStore.store_hours, function (value, key) {
                    //     var hour = vm.findHourById(value);
                    //     if(hour.day_of_week === 0){
                    //         hour.order = 7;
                    //     }
                    //     else {
                    //         hour.order = hour.day_of_week;
                    //     }
                    //     storeHours.push(hour);
                    // });
                    //     this.storeHours = _.sortBy(storeHours, [function(o) { return o.order; }]);
                    // setTimeout(function() {
                    //     vm.addLandmark(vm.currentStore);
                    // }, 500);
                },
                locale: function(val, oldVal) {
                    console.log("locale", this.locale);
                },
            },
            
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'processedStores',
                    'findStoreBySlug',
                    'findPromoById',
                    'findCategoryById'
                ]),
                storeCategory() {
                    var currentStoreCategory = this.currentStore.categories[0];
                    category = this.findCategoryById(currentStoreCategory)
                    return category.name
                },
                getPNGurl () {
                    return "https://www.mallmaverick.com" + this.property.map_url;
                },
                // svgMapRef() {
                //     return _.filter(this.$children, function(o) {
                //         return (o.$el.className == "svg-map")
                //     })[0];
                // },
            },
            methods: {
                loadData: async function() {
                    try {
                        // avoid making LOAD_META_DATA call for now as it will cause the entire Promise.all to fail since no meta data is set up.
                        let results = await Promise.all([this.$store.dispatch("getData","categories")]);
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
                updateCurrentStore (id) {
                    this.currentStore = this.findStoreBySlug(id);
                    if (this.currentStore === null || this.currentStore === undefined){
                        this.$router.replace({ name: '404'});
                    }
                },
                // updateSVGMap(map) {
                //     this.map = map;
                // },
                // addLandmark(store) {
                //     this.svgMapRef.addMarker(store);
                // },
            }
        });
    });
</script>