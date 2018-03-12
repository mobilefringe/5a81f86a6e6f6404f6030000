<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loader v-if="!dataLoaded"></loader>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak>
                <div class="page_header" v-if="pageBanner" v-bind:style="{ backgroundImage: 'url(' + pageBanner.image_url + ')' }">
        			<div class="site_container">
        				<div class="header_content caps">
        				    <p>{{ $t("map_page.header_desc") }} {{ property.name }}</p>
        					<h1>{{$t("map_page.header_title")}}</h1>
        				</div>
        			</div>
        		</div>  
                <div class="site_container">
                    <div class="row">
                        <div class="col-md-12">
                            <div class="alpha_list hidden_phone">
                                <a @click="filterStores('All')" class="all_a">All</a>
                                <a @click="filterStores('#')">#</a>
                                <a v-for="letter in alphabet" @click="filterStores(letter)">{{letter}}</a>
                            </div>
                            <div class="margin_40"></div>
                        </div>
                    </div>
                    <div class="row">
                        <div class="col-md-3">
                            <div class="map_search_container">
                                <search-component v-model="storeSearch" :list="processedStores" :suggestion-attribute="suggestionAttribute" @select="onOptionSelect" placeholder="Search Store Name">
                                    <template slot="item" scope="option">
                                        <article class="media"><p>{{ option.data.name }}</p></article>
                                    </template>
                                </search-component>
                                <i id="store_search_icon" class="fa fa-search" aria-hidden="true"></i>
                            </div>
                            <div class="store_list">
                                <div class="store_list_container hidden-mobile" v-if="filteredStores">
                                    <div class="store_name" v-for="store in filteredStores">
                                        <p @click="dropPin(store)">{{store.name}}</p>
                                    </div>
                                </div>
                                <div v-else>
                                    <div class="store_name">
                                        <p>{{ $t("map_page.no_stores") }}</p>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="margin-30 visible-mobile"></div>
                        <div class="col-md-9">
                            <!--<mapplic-map ref="mapplic_ref" :height="700" :minimap= "true" :deeplinking="false" :sidebar="false" :hovertip="true" :maxscale= "5" :storelist="allStores" :floorlist="floorList" tooltiplabel="Info"></mapplic-map>-->
                        </div>
                    </div>
                </div>
            </div>
        </transition>
    </div>
</template>
<script>
    define(["Vue", "vuex", "vue!mapplic-map"], function(Vue, Vuex, MapplicComponent) {
        return Vue.component("stores-component", {
            template: template, // the variable template will be injected
            data: function () {
                return {
                    dataLoaded: false,
                    pageBanner: null,
                    alphabet : [
                        'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z'
                    ],
                    suggestionAttribute: "name",
                    storeSearch: null,
                    filteredStores: null,
                }
            },
            created() {
                this.loadData().then(response => {
                    // var temp_repo = this.findRepoByName('Map Banner');
                    // if(temp_repo) {
                    //     this.pageBanner = temp_repo.images[0];
                    // }
                    
                    this.filteredStores = this.processedStores;
                    this.dataLoaded = true;
                });
            },
            // watch: {
            //     selected: function() {
            //         console.log(this.selected.value)
            //         var catName = this.selected.value;
            //         var storesByCategory = this.storesByCategoryName
            //         var sortedList = _.uniq(storesByCategory[catName]);
            //         if(this.selected.value == undefined){
            //             this.currentSelection = this.allStores;
            //         } else {
            //             this.currentSelection = sortedList;
            //         }
            //     },
            // },
            computed: {
                ...Vuex.mapGetters([
                    "property",
                    "timezone",
                    "repos",
                    "findRepoByName",
                    "processedStores",
                    'storesByAlphaIndex',
                ]),
                allStores() {
                    return this.processedStores;
                },
                floorList () {
                    var floor_list = [];
                    
                    var floor_1 = {};
                    floor_1.id = "first-floor";
                    floor_1.title = "Level One";
                    floor_1.map = "//mallmaverick.com/system/site_images/photos/000/035/861/original/NorthPark_-_Dec-15-2017_-_Floor_1.svg";
                    floor_1.minimap = "//codecloud.cdn.speedyrails.net/sites/5a4bb6d36e6f6473fa0a0000/image/png/1513365138000/NorthPark - Dec-15-2017 - Floor 1.png";
                    floor_1.z_index = 1;
                    floor_1.show = true;
                    
                    floor_list.push(floor_1);
                    var floor_2 = {};
                    floor_2.id = "second-floor";
                    floor_2.title = "Level Two";
                    floor_2.map = "//mallmaverick.com/system/site_images/photos/000/035/873/original/NorthPark_-_Dec-15-2017_-_Floor_2.svg";
                    floor_2.minimap = "//codecloud.cdn.speedyrails.net/sites/5a4bb6d36e6f6473fa0a0000/image/png/1513365146000/NorthPark - Dec-15-2017 - Floor 2.png";
                    floor_2.z_index = 2;
                    floor_2.show = false;
                    
                    floor_list.push(floor_2);
                    return floor_list;
                }
            },
            methods: {
                loadData: async function() {
                    try {
                        let results = await Promise.all([this.$store.dispatch("getData", "repos")]);
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
                filterStores (letter) {
                    console.log(letter)
                    if(letter == "All" || letter == undefined || letter == null){
                        this.filteredStores = this.processedStores;
                    } else {
                        var filtered = _.filter(this.storesByAlphaIndex, function(o,i) { return _.lowerCase(i) == _.lowerCase(letter); })[0];
                        // if(filtered != undefined) {
                            this.filteredStores = filtered
                        // } else {
                        //     this.noStores = true;
                        // }
                        // this.filteredStores = _.groupBy(filtered, store => (isNaN(store.name.charAt(0)) ? store.name.charAt(0) : "#"));
                    }
                },
                onOptionSelect(option) {
                    this.$nextTick(function() {
                        this.storeSearch = ""
                    });
                    this.$refs.mapplic_ref.showLocation(option.svgmap_region);
                },
                // mapDownload: function mapDownload() {
                //     var repo = this.findRepoByName("maps").images;
                //     var map = _.filter(repo, function(o) {
                //         return o.id == "31625";
                //     });
                //     var mapURL = "http://www.mallmaverick.com" + map[0].photo_url;
                //     return mapURL;
                // },
                dropPin(store) {
                    this.$refs.mapplic_ref.showLocation(store.svgmap_region);
                }
            }
            
        });
    });
</script>