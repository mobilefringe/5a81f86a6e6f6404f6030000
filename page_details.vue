<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loader v-if="!dataLoaded"></loader>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak>
                <div class="site_container">
    				<div class="pages_content">
    					<div class="page_body description_text text_left" v-if="locale=='en-ca'" v-html="currentPage.body"></div>
                        <div class="page_body description_text text_left" v-else v-html="currentPage.body_2"></div>
    				</div>
    			</div>
			</div>
		</transition>
    </div>
</template>
>
<script>
    define(["Vue", "vuex"], function(Vue, Vuex) {
        return Vue.component("page-details-component", {
            template: template, // the variable template will be injected,
            data: function() {
                return {
                    dataLoaded: false,
                    currentPage: null
                }
            },
            props:['id', 'locale'],
            // beforeRouteUpdate(to, from, next) {
            //     this.loadData(to.params.id).then(response => {
            //         this.currentPage = response[0].data;
            //         var temp_repo = this.findRepoByName('Pages Banner');
            //         if(temp_repo) {
            //             this.pageBanner = temp_repo.images[0];
            //         }
            //         this.pageBanner = this.pageBanner;
                    
            //     });
            //     next();
            // },
            created(){
                this.loadData(this.id).then(response => {
                    this.currentPage = response[0].data;
                    this.dataLoaded = true;
                });
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'findRepoByName'
                ])
            },
            methods: {
                updateCurrentPage(id) {
                    var _this = this;
                    this.$store.dispatch('LOAD_PAGE_DATA', { url: this.property.mm_host + "/pages/" + this.id + ".json" }).then(function (response) {
                        _this.currentPage = response.data;
                        _this.dataLoaded = true;
                    }, function (error) {
                        console.error( "Could not retrieve data from server. Please check internet connection and try again.");
                        _this.$router.replace({ name: 'home' });
                    });
                }
            }
        });
    });
</script>