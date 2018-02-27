<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loader v-if="!dataLoaded"></loader>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak>
        		<div class="site_container">
				    <div class="job_details_container clearfix">
					    <div class="promo_img" v-lazy:background-image="currentJob.image_url"></div>
					    <div class="promo_content">
					        <h3 class="">{{ currentJob.store_name }}</h3>
					        <p v-if="currentJob.store_category">{{ currentJob.store_category }}</p>
							<p></p>
							<hr>
					        <p class="job_position">{{ $t("jobs_page.position") }}: {{ currentJob.job_type }}</p>
							<p class="job_date">{{ $t("jobs_page.end_date") }}: {{ currentJob.end_date | moment("MMMM DD, YYYY", timezone)}}</p>
					    </div>
					    <div class="job_details_desc">
        				    <div v-html="currentJob.description"></div>
        				    <a v-if="currentJob.website" :href="'//' + currentJob.website" target="_blank">
        				        <div class="details_store_website">{{$t("stores_page.store_website")}}</div>
        				    </a>
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
        return Vue.component("job-details-component", {
            template: template, // the variable template will be injected,
            data: function() {
                return {
                    dataLoaded: false,
                    currentJob: null,
                    storeJobs : null,
                    storeHours : null,
                }
            },
            props:['id', 'locale'],
            beforeRouteUpdate(to, from, next) {
                this.currentJob = this.findJobBySlug(to.params.id);
                    if (this.currentJob === null || this.currentJob === undefined){
                        this.$router.replace({ name: '404'});
                    }
                next();
            },
            created(){
                this.loadData().then(response => {
                    this.dataLoaded = true;
                    this.updateCurrentJob(this.id);
                    
                    var temp_repo = this.findRepoByName('Jobs Banner');
                    if(temp_repo) {
                        this.jobBanner = temp_repo.images[0];
                    }
                    console.log(this.jobBanner);
                    this.jobs = this.job;
                });
            },
            watch: {
                currentJob : function (){
                    if(this.currentJob != null) {
                        console.log(this.currentJob.store);
                        if (this.currentJob.store != null && this.currentJob.store != undefined && _.includes(this.currentJob.store.image_url, 'missing')) {
                            this.currentJob.store.image_url = "http://via.placeholder.com/400x400/757575";
                        }
                        else if (this.currentJob.store == null || this.currentJob.store == undefined) {
                            this.currentJob.store = {};
                            this.currentJob.store.image_url =  "http://via.placeholder.com/400x400/757575";
                        }
                        var vm = this;
                        var temp_job = [];
                        var current_id =_.toNumber(this.currentJob.id);
                        _.forEach(this.currentJob.store.jobs, function(value, key) {
                            if(_.toNumber(value) != current_id){
                                var current_promo = vm.findJobById(value);
                                current_promo.description_short = _.truncate(current_promo.description, {'length': 70});
                                temp_job.push(current_promo);
                            }
                        });
                        this.storeJobs = temp_job;
                    }
                    if(this.currentJob.store) {
                        var storeHours = [];
                        var vm = this;
                        _.forEach(this.currentJob.store.store_hours, function (value, key) {
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
                    'processedJobs',
                    'findJobBySlug',
                    'findJobById',
                    'timezone',
                    'findRepoByName',
                    'findHourById'
                ]),
                allJobs() {
                    return this.processedJobs;
                },
            },
            methods: {
                updateCurrentJob (id) {
                    this.currentJob = this.findJobBySlug(id);
                    if (this.currentJob === null || this.currentJob === undefined){
                        this.$router.replace({ name: '404'});
                    }
                },
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