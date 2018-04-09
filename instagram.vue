<template>
    <div v-if="dataLoaded" class="insta_feed_container">
            
    </div>
</template>

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