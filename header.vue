<template>
    <header>
        <div class="sticky">
			<div class="site_container">
				<div class="row header_logo">
					<div class="col-md-3 hidden_phone">
					    <div class="language_select">
					        <span @click="changeLocale('en-ca')">en</span> | <span @click="changeLocale('fr-ca')">fr</span>
				        </div>	
					</div>
					<div class="col-xs-12 col-md-6 mobile_header">
					    <div class="property_logo center-block">
							<router-link to="/">
							    <img src="//codecloud.cdn.speedyrails.net/sites/5a81f86a6e6f6404f6030000/image/png/1519154972000/mm_logo.png" alt="Property Logo"/>
						    </router-link>
						</div>
						<div class="mobile_menu_icon visible_phone" @click="showMenu = !showMenu">
					        <span>Menu</span>
				            <i v-if="!showMobileMenu" class="fa fa-angle-down"></i>
				            <i v-if="showMobileMenu" class="fa fa-angle-up"></i>
					    </div>
					</div>
					<div class="col-md-3 hidden_phone">
					    <div class="header_search_container">
    					    <search-component v-model="search" :list="processedStores" :suggestion-attribute="suggestionAttribute" @select="onOptionSelect">
                                <template slot="item" scope="option">
                                    <article class="media">
                                        <p>{{ option.data.name }}</p>
                                    </article>
                                </template>
                            </search-component>	
                        </div>
					</div>
				</div>
				<div class="row">
				    <div class="col-md-2 hidden_phone"></div>
					<div class="col-sm-12 col-md-8">
				        <div class="nav_container">
					        <!--<transition name="custom-classes-transition" enter-active-class="animated slideInDown" leave-active-class="animated slideOutUp">-->
        						<nav id="primary_nav"  :class="{ slideInDown: showMenu, slideOutUp: !showMenu }">
        							<ul>
        							    <li v-for="item in menu_items" class="menu_item" @click="toggleSubMenu(item.id)">
        							        <router-link :to="item.href">{{$t(item.name)}}</router-link>
        							        <ul v-if="item.sub_menu" :class="{ show_submenu: showSubMenu }">
        							            <li v-for="sub_menu in item.sub_menu" class="dropdown_item">
        							                <router-link :to="sub_menu.href">{{$t(sub_menu.name)}}</router-link>
        							            </li>
        									</ul>
        							    </li>
        							</ul>
        							<div class="mobile_nav_content visible_phone">
        							    <div class="social_icons">
                                            <span v-for="item in social_media">
                                                <a :href="item.url" target="_blank">
                                                    <i :class="item.iconClass" aria-hidden="true"></i>
                                                </a>
                                            </span>
                                        </div> 
                                        <div class="center">
                                            <p>{{ property.name }}<br>
                                                <a href="" target="_blank">{{ property.address1 }} {{ property.city }} {{ property.province_state }} {{ property.postal_code }}</a>
                                            </p>
                                            
                                        </div>
        							</div>
        						</nav>
    					    <!--</transition>-->
    					</div>
					</div>
					<div class="col-md-2 hidden_phone">
					    <div class="social_icons pull-right">
                            <span v-for="item in social_media">
                                <a :href="item.url" target="_blank">
                                    <i :class="item.iconClass" aria-hidden="true"></i>
                                </a>
                            </span>
                        </div>	
					</div>
				</div>
			</div>
		</div>
    </header>
</template>

<script>
    define(["Vue", "vuex", "vue_router", "routes"], function (Vue, Vuex, VueRouter, appRoutes) {
        return Vue.component("header-component", {
            template: template, // the variable template will be injected,
            props:['menu_items', 'social_media'],
            data: function () {
                return {
                    suggestionAttribute: 'name',
                    search: '', 
                    showMenu: false,
                    isMobile: false,
                    showMobileMenu: false,
                    showSubMenu: false,
                    noScroll: false,
                    windowWidth: 0
                }
            },
            watch: {
                $route: function() {
                    if (this.windowWidth <= 768) {
                        this.showMenu = false;
                    }  
                },
                windowWidth: function() {
                    if (this.windowWidth >= 768) {
                        document.body.classList.remove("no-scroll");
                        this.showMenu = true;
                    } else {
                        this.showMenu = false;
                    }
                },
                showMenu: function() {
                    if(this.showMenu == true){
                        if (this.windowWidth <= 768) {
                            document.body.classList.add("no-scroll");
                        }
                    } else if (this.showMenu == false) {
                        document.body.classList.remove("no-scroll");
                    }
                }
            },
            created() {
                this.$nextTick(function() {
                    window.addEventListener('resize', this.getWindowWidth);
                    this.getWindowWidth();
                });
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'processedStores'
                ]),
                locale: {
                    get () {
                        return this.$store.state.locale
                    },
                    set (value) {
                        this.$store.commit('SET_LOCALE', { lang: value })
                    }
                }
            },
            methods: {
                changeLocale: function(val) {
                    // this will update the data store, which in turn will trigger the watcher to update the locale in the system
                    this.locale = val; 
                },
                getWindowWidth(event) {
                    this.windowWidth = window.innerWidth;
                },
                toggleSubMenu(id) {
                    console.log(id)
                    // this.showSubMenu1 = false;
                    // this.showSubMenu2 = false;
                    // this.showSubMenu3 = false;
                    
                    // if(id == "dropDown1"){
                    //     this.showSubMenu1 = true   
                    // } else if (id == "dropDown2"){
                    //     this.showSubMenu2 = true 
                    // } else if (id == "dropDown3"){
                    //     this.showSubMenu3 = true 
                    // }
                    
                },
                onOptionSelect(option) {
                    console.log('Selected option:', option);
                    this.$nextTick(function() {
                        this.search = ""
                    });
                    this.$router.push("/stores/" + option.slug);
                }
            },
            beforeDestroy: function() {
                window.removeEventListener('resize', this.getWindowWidth);
            }
        });
    });
</script>