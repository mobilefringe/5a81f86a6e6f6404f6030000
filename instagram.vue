<template>
    <div class="row">
        <div class="col-md-6">
            <div v-if="dataLoaded" class="insta_feed_container">
                <div class="insta_header" v-if="instaUser">
                    <a href="">
                        <div>
                            <h3>{{ instaUser.user.username }}</h3>
                        </div>
                        <div class="profile_pic">
                            <img :src="instaUser.user.profile_picture"/>
                        </div>
                    </a>
                </div>
                <div v-for="item in instaFeed" class="insta_item">
                    <div class="photo_wrap">
                        <a :href="item.link" target="_blank">
                            <i v-if="item.type == 'video'" class="fa fa-play play_btn"></i> 
                            <img :src="item.images.thumbnail.url"/>
                        </a>
                    </div>
                    <div class="info">
                        <span class="likes">
                            <i class="fa fa-heart" style="font-size: 13px;"></i>
                            {{ item.likes.count }}
                        </span>
                        <span class="comments">
                            <i class="fa fa-comment" style="font-size: 13px;"></i>
                            {{ item.comments.count }}
                        </span>
                    </div>
                </div>        
            </div>    
        </div>
    </div>
    
</template>

<style>
    .insta_feed_container {
        max-width: 500px;
    }
    .insta_header {
        
    }
    .profile_pic {
        max-width: 50px;
        max-height: 50px;
    }
    .profile_pic img{
        max-width: 100%;
        border-radius: 50%;
    }
    .insta_item {
        display: -moz-inline-stack;
        display: inline-block;
        vertical-align: top;
        zoom: 1;
        padding: 5px;
        margin: 0 !important;
        text-decoration: none;
        -webkit-box-sizing: border-box;
        -moz-box-sizing: border-box;
        box-sizing: border-box;
        width: 33.33%;
    }    
.photo_wrap {
    position: relative;
}
.play_btn {
    display: block !important;
    position: absolute;
    z-index: 0;
    top: 50%;
    left: 50%;
    margin-top: -24px;
    margin-left: -19px;
    padding: 0;
    font-size: 48px;
    color: #fff;
    color: rgba(255,255,255,0.9);
    font-style: normal !important;
    font-size: 23px;
    margin-top: -12px;
    text-shadow: 0 0 8px rgba(0,0,0,0.8);
    margin-left: -9px;
}
.photo_wrap a {
    display: block;
    text-decoration: none;
}
.photo_wrap img {
    display: block;
    padding: 0 !important;
    margin: 0 !important;
    max-width: 100% !important;
    opacity: 1 !important;
    font-size: 10px !important;
    line-height: 0.9;
    color: #999;
    border: 6px solid white;
}
.info {
    width: 100%;
    float: left;
    clear: both;
    text-decoration: none;
    color: #666;
    text-align: center;
        line-height: 1.1;
    padding: 4px 0 8px 0;
    background: #FFF;
}
</style>

<script>
    define(["Vue", "vue!vue-slick"], function (Vue, slick) {
        return Vue.component("instagram", {
            template: template, // the variable template will be injected,
            data: function () {
                return {
                    dataLoaded: false,
                    instaUser: null,
                    instaFeed: null,
                };
            },
            created () {
                this.loadData().then(response => {
                    var instaFeed = response[0].data;
                    var insta_feed = instaFeed.social.instagram;
                    this.instaUser = insta_feed[0];
                    console.log(this.instaUser)
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