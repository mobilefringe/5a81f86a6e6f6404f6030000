<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loader v-if="!dataLoaded"></loader>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak>
        		<!--<div class="page_header" v-if="pageBanner" v-bind:style="{ backgroundImage: 'url(' + pageBanner.image_url + ')' }">-->
        		<div class="page_header"  v-bind:style="{ backgroundImage: 'url(http://via.placeholder.com/1920x600)' }">
        			<div class="site_container">
        				<div class="header_content caps">
        				    <p>{{ $t("jobs_page.jobs_header_desc") }} {{ property.name }}</p>
        					<h1>{{ $t("jobs_page.jobs") }}</h1>
        				</div>
        			</div>
        		</div>
        		<div class="site_container page_content">
        			<div id="events_container" v-if="promotions.length > 0">
        				<paginate name="promos" v-if="promos" :list="promos" class="paginate-list margin-60" :per="4">
        				    <div class="promo_container" v-for="(promo, index) in paginated('promos')">
        					    <div class="job_img" v-lazy:background-image="promo.image_url"></div>
        					    <div class="job_content">
        					        
        					        <h3 class="">{{ promo.name_short }}</h3>
        							<p></p>
        							<hr>
        					        <p class="promo_desc"  v-if="locale=='en-ca'" >{{ promo.description_short }}</p>
        							<p class="promo_desc" v-else>{{ promo.description_short_2 }}</p>
        							<router-link :to="'/promotions/'+ promo.slug" >
        								   <div class="promo_learn_more animated_btn">{{ $t("promos_page.read_more") }}</div>
        						    </router-link>
        					    </div>
        					</div>
        					
        					<div class="row event_container" v-for="(promo,index) in paginated('promos')" :class="{ 'last': index === (paginated('promos').length - 1) }">
        						<div class="col-sm-6 col-md-4 event_image_container">
        							<router-link :to="'/jobs/'+ promo.slug" class="event_learn_more">
        								<img v-lazy="promo.store.image_url"  class="event_image image" alt=""/>
        							</router-link>
        						</div>
        						<div class="col-sm-6 col-md-8 event_dets_container">
        							<h4 class="event_name caps"  v-if="locale=='en-ca'">{{promo.name}}</h4>
        							<h4 class="event_name caps"  v-else>{{promo.name_2}}</h4>
        							<div v-if="promo.jobable_type == 'Store'">
            						    <h4 class="event_store_name caps" v-if="locale=='en-ca'">{{promo.store.name}}</h4>
            						    <h4 class="event_store_name caps" v-else>{{promo.store.name_2}}</h4>
            						</div>
        							<div class="event_thick_line"></div>
        							<p class="event_dates">{{promo.start_date | moment("MMM D", timezone)}} - {{promo.end_date | moment("MMM D", timezone)}}</p>
        							<p class="event_desc"  v-if="locale=='en-ca'">{{promo.description_short}}</p>
        							<p class="event_desc"  v-else>{{promo.description_short_2}}</p>
        							<div class="text-right  col-sm-6" v-if="promo" style="padding:0">
        								<router-link :to="'/jobs/'+ promo.slug" class="event_learn_more pull-left">
        									{{$t("jobs_page.read_more")}} <i class="fa fa-angle-right" aria-hidden="true"></i>
        								</router-link>
        								
        							</div>
        						</div>
        						<div class="col-sm-12">
        							<hr>
        						</div>
        					</div>
        				</paginate>
        			</div>
        			<div id="no_events" class="row" v-else>
        				<div class="col-md-12">
        					<p>{{$t("jobs_page.no_job_message")}}</p>
        				</div>
        			</div>
        			<div class="row margin-60">
        				<div class="col-md-12">
        					<paginate-links for="promos" :async="true" :limit="5" :show-step-links="true"></paginate-links>
        				</div>
        			</div>
        		</div>
		    </div>
		</transition>
	</div>
</template>

<script>
    define(["Vue", "vuex", "moment", "moment-timezone", "vue-moment", "vue-meta", "vue-lazy-load", "vue-paginate"], function(Vue, Vuex, moment, tz, VueMoment, Meta, VueLazyload, VuePaginate) {
        Vue.use(Meta);
        Vue.use(VueLazyload);
        Vue.use(VuePaginate);
        return Vue.component("promos-component", {
            template: template, // the variable template will be injected
            props:['locale'],
            data: function() {
                return {
                    dataLoaded: false,
                    promoBanner: null,
                    paginate: ['promos'],
                    promos : null
                }
            },
            created() {
                this.loadData().then(response => {
                    this.dataLoaded = true;
                    
                    // var temp_repo = this.findRepoByName('Jobs Banner');
                    // if(temp_repo) {
                    //     this.promoBanner = temp_repo.images[0];
                    // }
                    // console.log(this.promoBanner);
                    this.promos = this.promotions;
                });
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'findRepoByName',
                    'processedJobs'
                ]),
                promotions() {
                    var vm = this;
                    var temp_promo = [];
                    var temp_job = [];
                    _.forEach(this.processedJobs, function(value, key) {
                        value.description_short = _.truncate(value.description, {
                            'length': 150
                        });
                        value.description_short_2 = _.truncate(value.description_2, {
                            'length': 150
                        });
                        if (value.store != null && value.store != undefined && _.includes(value.store.image_url, 'missing')) {
                            value.store.image_url = "http://via.placeholder.com/400x400/757575";
                        }
                        else if (value.store == null || value.store == undefined) {
                            value.store = {};
                            value.store.image_url =  "http://via.placeholder.com/400x400/757575";
                        }
                        if (_.includes(value.image_url, 'missing')) {
                            value.image_url = "http://via.placeholder.com/400x400/757575";
                        }
                        // value.image_url = "//codecloud.cdn.speedyrails.net/sites/5a6a54eb6e6f647da51e0100/image/png/1516652189884/ES_logo_red2.png";
                        
                        temp_promo.push(value);
                    });
                    _.sortBy(temp_promo, [function(o) { return o.start_date; }]);
                    return temp_promo;
                },
            },
            methods: {
                loadData: async function() {
                    try {
                        // avoid making LOAD_META_DATA call for now as it will cause the entire Promise.all to fail since no meta data is set up.
                        let results = await Promise.all([this.$store.dispatch("getData", "jobs"), this.$store.dispatch("getData", "repos")]);
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
                shareURL(slug){
                    var share_url = "http://mallmaverick.ca/jobs/" + slug;
                    return share_url;
                },
            }
        });
    });
</script>