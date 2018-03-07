<template>
	<div class="row page_content" v-if="dataLoaded">
		<div class="banner_div">
			<div class="home_banner_container">
				<slick ref="slick" :options="slickOptions">
					<div class="" v-for="banner in banners" v-if="banners">
						<!--<img :src="banner.image_url" class="banner_img" alt="">-->
						<div class="home_banner" v-bind:style="{ backgroundImage: 'url(' + banner.image_url + ')' }">
						    <div class="home_banner_content" v-if="banner.name && banner.description">
						        <h1>{{ banner.name }}</h1>
						        <p>{{ banner.description }}</p>
						    </div>   
						</div>
					</div>
				</slick>
			</div>
		</div>
		<div class="site_container">
		    <div>
		      <h3 class="home_page_title caps">{{$t("home_page.explore")}}</h3>
		    </div>
		    <div v-masonry transition-duration="0.3s" item-selector=".grid-item" class="hidden_phone">
                <div v-masonry-tile class="item" >
                    <div v-for="feature in feature_items" :class="'grid-item ' + feature.masonry_class ">
                    	<div class="ih-item circle effect19">
                    		<a href="">
                    			<img :src="feature.image_url" alt="name">
                    			<div class="info">
                    				<div class="content">
                    					<h3 v-if="locale=='en-ca'"> {{feature.name}} </h3>
                    					<h3 v-else> {{feature.name_2}} </h3>
                    				</div>
                    			</div>
                    		</a>
                    	</div>
                    </div>
                </div>
            </div>
            <div v-masonry transition-duration="0.3s" item-selector=".grid-item" class="visible_phone">
                <div v-masonry-tile class="item" >
                    <div v-for="feature in mobile_feature_items" :class="'grid-item ' + feature.masonry_class ">
                    	<div class="ih-item circle effect19" >
                    		<a href="">
                    			<img :src="feature.image_url" alt="name">
                    			<div class="info">
                    				<div class="content">
                    					<h3 v-if="locale=='en-ca'"> {{feature.name}} </h3>
                    					<h3 v-else> {{feature.name_2}} </h3>
                    				</div>
                    			</div>
                    		</a>
                    	</div>
                    </div>
                </div>
            </div>
            <div>
		      <h3 class="home_page_title caps">{{$t("home_page.our_feed")}}</h3>
		    </div>
		    <div class="insta-feed-container">
                <div class="insta-feed-image col-xs-6 col-sm-3" v-for="(item, index) in instaFeed">
                    <a :href="item.link" target="_blank"><img :src="item.images.thumbnail.url"/></a>
                </div>
            </div>
            
		</div>
	</div>
</template>

<script>
    define(["Vue", "vuex", "vue!today_hours", "vue!search-component", 'vue!vue-slick', 'js-cookie', 'masonry' , 'vue-masonry-plugin'], function(Vue, Vuex, TodayHoursComponent, SearchComponent, slick, Cookies, masonry, VueMasonryPlugin) {
        Vue.use(VueMasonryPlugin.default);
        return Vue.component("home-component", {
            template: template, // the variable template will be injected
            props:['locale'],
            data: function() {
                return {
                    suggestionAttribute: 'name',
                    search: '',
                    slickOptions: {
                        arrows: false,
                        autoplay: true,
                        autoplaySpeed: 6000,
                        cssEase: 'linear',
                        dots: true,
                        fade: true,
                        infinite: true,
                        slidesToShow: 1,
                        speed: 1600
                    },
                    dataLoaded: false,
                    show_popup: false,
                    popup: null,
                    formData : {},
                    instaFeed: null
                }
            },
            created () {
                this.loadData().then(response => {
                    this.dataLoaded = true;
                    this.popup = this.$store.state.popups[0];
                    
                    console.log(response);
                    var socialFeed = response[4].data;
                    console.log("socialFeed", socialFeed);
                    var social_feed = socialFeed.social.instagram;
                    this.instaFeed = _.slice(social_feed, [0], [4]);
                    this.instaFeed.map(insta => {
                            insta.images.thumbnail.url = "http://via.placeholder.com/400x400";
                    });
                    // this.instaFeed = insta_feed
                    console.log("locale created", this.locale);
                });
            },
            watch : {
                dataLoaded () {
                    var viewed = Cookies.get('popup_viewed');
                    if(this.popup !== null && viewed !== "true") {
                        Cookies.set('popup_viewed', "true");
                        viewed = Cookies.get('popup_viewed');
                        this.show_popup = true;
                        this.popup.image_url = "//mallmaverick.cdn.speedyrails.net" + this.popup.photo_url;
                        document.getElementById('popup_backdrop').style.display = "block";
                    }
                    else {
                        document.getElementById('popup_backdrop').style.display = "none";
                    }
                },
                formData () {
                    this.formData.name = this.formData.firstname + " " + this.formData.lastname; 
                },
                locale: function(val, oldVal) {
                    console.log("locale", this.locale);
                },
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'processedStores'
                ]),
                banners () {
                    console.log(this.$store.state.banners)
                    return _.orderBy(this.$store.state.banners, ['position'], ['asc']);
                },
                feature_items () {
                    var features = this.$store.state.feature_items;
                    _.forEach(features, function(value, key) {
                        if ( _.includes([1, 2], key) ) {
                            value.masonry_class = "grid-item--width2";
                        } else if ( _.includes([3], key) ){
                            value.masonry_class = "grid-item--height2";
                        } else {
                            value.masonry_class = " ";
                        }
                    });
                    return features;
                },
                mobile_feature_items () {
                    var features = this.$store.state.feature_items;
                    _.forEach(features, function(value, key) {
                      
                        if( _.includes([1, 4], key) ) {
                            value.masonry_class = "grid-item--height2";
                        }
                        else if ( _.includes([5], key) ){
                            value.masonry_class = "grid-item--width2";
                        }
                        else {
                            value.masonry_class = " ";
                        }
                        if(key == 8) {
                            value.mobile_order = 6;
                        }
                        else if(key < 5) {
                            value.mobile_order = key + 1;
                        }
                        else if(key >= 5) {
                            value.mobile_order = key + 2;
                        }
                    });
                    features = _.sortBy(features, [function(o) { return o.mobile_order; }]);
                    // _.forEach(features, function(value, key) {
                    //     value.mobile_order;
                    //     });
                        console.log(features);
                    return features;
                }
            },
            methods: {
                loadData: async function() {
                    try {
                        // avoid making LOAD_META_DATA call for now as it will cause the entire Promise.all to fail since no meta data is set up.
                        let results = await Promise.all([this.$store.dispatch("getData", "banners"), this.$store.dispatch("getData", "feature_items"), this.$store.dispatch("getData", "promotions"), this.$store.dispatch("getData", "popups"), this.$store.dispatch('LOAD_PAGE_DATA', {url: "http://northside.mallmaverick.com/api/v2/northside/social.json"})]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
                closePopup() {
                    this.show_popup = false;
                    document.getElementById('popup_backdrop').style.display = "none";
                }
            }
        })
    })
</script>