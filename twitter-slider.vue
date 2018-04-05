<template>
    <div v-if="dataLoaded" class="twitter_feed_container">
        <div class="prev"></div>
        <slick ref="slick" :options="slickOptions">
			<div class="twitter_item" v-for="(item, index) in twitterFeed" :class="{ 'first': index === 0, 'last': index === (twitterFeed - 1) }">
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
		<div class="next"></div>
    </div>
</template>

<script>
    define(["Vue", "vuex"], function (Vue, Vuex) {
        return Vue.component("twitter-slider", {
            template: template, // the variable template will be injected,
            data: function () {
                return {
                    dataLoaded: false,
                    twitterFeed: null,
                    slickOptions: {
                        arrows: true,
                        autoplay: false,
                        // autoplaySpeed: 6000,
                        cssEase: 'linear',
                        dots: false,
                        // fade: true,
                        // infinite: true,
                        nextArrow: '.next',
                        prevArrow: '.prev',
                        slidesToShow: 3,
                        // speed: 1200
                    }
                };
            },
            created () {
                this.loadData().then(response => {
                    var twitterFeed = response[0].data;
                    var twitter_feed = twitterFeed.social.twitter;
                    this.twitterFeed = _.slice(twitter_feed, [0], [8]);
                    
                    this.dataLoaded = true;
                });
            },
            // computed: {
            //     ...Vuex.mapGetters([
            //         'property'
            //     ])
            // },
            methods: {
                loadData: async function() {
                    try {
                        let results = await Promise.all([this.$store.dispatch('LOAD_PAGE_DATA', {url: "http://billingsbridge.mallmaverick.com/api/v3/billings/social.json"})]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                }
            }
        });
    });
</script>