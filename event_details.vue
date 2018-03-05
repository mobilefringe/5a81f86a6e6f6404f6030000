<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loader v-if="!dataLoaded"></loader>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak>
        		<div class="site_container page_content">
        		    <div class="margin_60"></div>
        			<div id="promos_container">
    					<div class="promo_container">
    					    <div class="promo_content center">
    					        <p class="promo_title">{{ $t("promos_page.promotions") }}</p>
    					        <h3 class="margin_60" v-if="locale=='en-ca'">{{ currentEvent.name }}</h3>
    							<h3 class="margin_60" v-else>{{ currentEvent.name_2 }}</h3>
    							<p class="promo_desc">
    							    {{ currentEvent.start_date | moment("MMM D", timezone) }} - {{ currentEvent.end_date | moment("MMM D", timezone) }}
    							</p>
    					    </div>
    					    <div class="promo_img" v-if="locale=='en-ca'" v-lazy:background-image="currentEvent.image_url"></div>
    					    <div class="promo_img" v-else v-lazy:background-image="currentEvent.promo_image2_url_abs"></div>
    					    <div class="promo_details_desc">
            				    <div v-if="locale=='en-ca'" v-html="currentEvent.rich_description"></div>
            				    <div v-else v-html="currentEvent.rich_description_2"></div>
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
        return Vue.component("event-details-component", {
            template: template, // the variable template will be injected,
            data: function() {
                return {
                    currentEvent: null,
                    storeEvents : null,
                    storeHours : null,
                    eventBanner : null
                }
            },
            props:['id', 'locale'],
            beforeRouteUpdate(to, from, next) {
                this.currentEvent = this.findEventBySlug(to.params.id);
                    if (this.currentEvent === null || this.currentEvent === undefined){
                        this.$router.replace({ name: '404'});
                    }
                next();
            },
            created(){
                this.loadData().then(response => {
                    this.updatecurrentEvent(this.id);
                    var temp_repo = this.findRepoByName('Events Banner');
                    if(temp_repo) {
                        this.eventBanner = temp_repo.images[0];
                    }
                    console.log(this.eventBanner);
                    this.events = this.event;
                });
            },
            watch: {
                currentEvent : function (){
                    if(this.currentEvent != null) {
                        console.log(this.currentEvent.store);
                        if (this.currentEvent.store != null && this.currentEvent.store != undefined && _.includes(this.currentEvent.store.image_url, 'missing')) {
                            this.currentEvent.store.image_url = "http://via.placeholder.com/400x400/757575";
                        }
                        else if (this.currentEvent.store == null || this.currentEvent.store == undefined) {
                            this.currentEvent.store = {};
                            this.currentEvent.store.image_url =  "http://via.placeholder.com/400x400/757575";
                        }
                        var vm = this;
                        var temp_event = [];
                        var current_id =_.toNumber(this.currentEvent.id);
                        _.forEach(this.currentEvent.store.event, function(value, key) {
                            if(_.toNumber(value) != current_id){
                                var current_event = vm.findEventById(value);
                                current_event.description_short = _.truncate(current_event.description, {'length': 70});
                                temp_event.push(current_event);
                            }
                        });
                        this.storeEvents = temp_event;
                    }
                    if(this.currentEvent.store) {
                        var storeHours = [];
                        var vm = this;
                        _.forEach(this.currentEvent.store.store_hours, function (value, key) {
                            var hour = vm.findHourById(value);
                            if(hour.day_of_week === 0){
                                hour.order = 7;
                            }
                            else {
                                hour.order = hour.day_of_week;
                            }
                            storeHours.push();
                        });
                        this.storeHours = _.sortBy(storeHours, [function(o) { return o.order; }]);;
                    }
                }
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'processedEents',
                    'findEventBySlug',
                    'findEventById',
                    'timezone',
                    'findRepoByName',
                    'findHourById'
                ]),
                allEvents() {
                    return this.processedEvents;
                },
            },
            methods: {
                updatecurrentEvent (id) {
                    this.currentEvent = this.findEventBySlug(id);
                    if (this.currentEvent === null || this.currentEvent === undefined){
                        this.$router.replace({ name: '404'});
                    }
                },
                loadData: async function() {
                    try {
                        // avoid making LOAD_META_DATA call for now as it will cause the entire Promise.all to fail since no meta data is set up.
                        let results = await Promise.all([this.$store.dispatch("getData", "events"), this.$store.dispatch("getData", "repos")]);
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
                shareURL(slug){
                    var share_url = "http://mallmaverick.ca/event/" + slug;
                    return share_url;
                },
            }
        });
    });
</script>