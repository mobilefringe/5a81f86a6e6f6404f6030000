<template>
    <div v-if="dataLoaded" class="twitter_feed_container">
        <slick ref="slick" :options="twitterOptions">
			<div class="twitter_item" v-for="(item, index) in twitterFeed">
			    <blockquote>
			        <p>{{ item.text }}</p>
			    </blockquote>
			    <div class="twitter_content">
			        <!--<figure>-->
			        <!--    <img :src="item.picture"/>-->
			        <!--</figure>-->
			        <a href="" target="_blank"><h5 class="caps">{{ item.username }}</h5></a>
                    <p>{{ item.created_at }}</p>
			    </div>
			</div>
		</slick>   
    </div>
</template>

<script>
    define(["Vue", "vuex"], function (Vue, Vuex) {
        Vue.component("vue-simple-spinner", Spinner);
        return Vue.component("loader", {
            template: template, // the variable template will be injected,
            data: function () {
                return {
                    dataLoaded: false
                };
            },
            created () {
                this.loadData().then(response => {
                    var twitterFeed = response[3].data;
                    console.log(twitterFeed)
                    var twitter_feed = twitterFeed.social.twitter;
                    this.twitterFeed = _.slice(twitter_feed, [0], [8]);

                    this.dataLoaded = true;
                });
            computed: {
                ...Vuex.mapGetters([
                    'property'
                ])
            },
            methods: {
                loadData: async function() {
                    try {
                        this.$store.dispatch('LOAD_PAGE_DATA', {url: "http://billingsbridge.mallmaverick.com/api/v3/billings/social.json"})]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                }
            }
        });
    });
</script>