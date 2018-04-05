<template>
    <div v-if="dataLoaded" class="twitter_feed_container">
        <div class="prev"></div>
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
		<div class="next"></div>
    </div>
</template>

<style>
    .twitter_item blockquote {
        margin: 0 10px;
        padding-left: 0;
        padding: 30px;
        position: relative;
        border-left: none;
        border-radius: 7px;
        background: $primary;
        font-size: 1rem;
        font-weight: 300;
    }
    .twitter_item blockquote:after {
        content: "";
        position: absolute;
        top: 100%;
        left: 40px;
        border-top: 10px solid black;
        border-top-color: $primary;
        border-left: 10px solid transparent;
        border-right: 10px solid transparent;
    }
    .twitter_item blockquote:first-child {
        margin-left: 0;
    }
    .twitter_item blockquote:last-child {
        margin-right: 0;
    }
    .twitter_item blockquote p {
        color: #FFF;
    }
    
    .twitter_item .twitter_content {
        margin-left: 40px;
    }
    .twitter_item .twitter_content h5 {
        margin-top: 20px;
    }
</style>
<script>
    define(["Vue", "vuex"], function (Vue, Vuex) {
        return Vue.component("twitter-slider", {
            template: template, // the variable template will be injected,
            data: function () {
                return {
                    dataLoaded: false,
                    twitterFeed: null,
                    twitterOptions: {
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