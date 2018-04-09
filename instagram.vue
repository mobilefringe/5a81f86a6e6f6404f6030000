<template>
    <div v-if="dataLoaded" class="insta_feed_container">
        <div class="insta_item">
            <div class="photo_wrap">
                
            </div>
        </div>        
    </div>
</template>

<style>
    
</style>
insta_item {
    display: -moz-inline-stack;
    display: inline-block;
    vertical-align: top;
    zoom: 1;
    padding: inherit !important;
    margin: 0 !important;
    text-decoration: none;
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    box-sizing: border-box;
}
<script>
    define(["Vue", "vue!vue-slick"], function (Vue, slick) {
        return Vue.component("instagram", {
            template: template, // the variable template will be injected,
            data: function () {
                return {
                    dataLoaded: false,
                    instaFeed: null,
                };
            },
            created () {
                this.loadData().then(response => {
                    var instaFeed = response[0].data;
                    var insta_feed = instaFeed.social.instagram;
                    this.instaFeed = _.slice(insta_feed, [0], [9]);
                    console.log(this.instaFeed)
                    
                    this.dataLoaded = true;
                });
            },
            methods: {
                loadData: async function() {
                    try {
                        let results = await Promise.all([this.$store.dispatch('LOAD_PAGE_DATA', {url: "http://stlaurent.mallmaverick.com/api/v3/stlaurent/social.json"})]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                }
            }
        });
    });
</script>