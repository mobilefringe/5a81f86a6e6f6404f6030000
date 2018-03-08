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
					<div class="col-sm-12 col-md-6">
					    <div class="property_logo center-block">
							<router-link to="/">
							    <img src="//codecloud.cdn.speedyrails.net/sites/5a81f86a6e6f6404f6030000/image/png/1519154972000/mm_logo.png" alt="Property Logo"/>
						    </router-link>
						</div>
					</div>
					<div class="col-md-3 hidden_phone">
					    <search-component v-model="search" :list="processedStores" :suggestion-attribute="suggestionAttribute" @select="onOptionSelect">
                            <template slot="item" scope="option">
                                <article class="media">
                                    <p>{{ option.data.name }}</p>
                                </article>
                            </template>
                        </search-component>	
					</div>
				</div>
				<div class="row">
				    <div class="col-md-2 hidden_phone"></div>
					<div class="col-sm-12 col-md-8 nav_container">
						<nav id="primary_nav">
							<ul>
							    <li v-for="item in menu_items" class="menu_item">
							        <router-link :to="item.href">{{$t(item.name)}}</router-link>
							        <!--{{$t(item.name)}}-->
							        <ul v-if="item.sub_menu">
							            <li v-for="sub_menu in item.sub_menu" class="dropdown_item">
							                <router-link :to="sub_menu.href">{{$t(sub_menu.name)}}</router-link>
							            </li>
									</ul>
							    </li>
							</ul>
						</nav>
					</div>
					<div class="col-md-2 hidden_phone">
					    <div class="social_icons">
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


						<!--<div id="menu-icon" @click="show_mobile_menu = !show_mobile_menu" :class="{ open: show_mobile_menu}">-->
						<!--	<span></span>-->
						<!--	<span></span>-->
						<!--	<span></span>-->
						<!--	<span></span>-->
						<!--</div>-->
	<!--			<div class="mobile_nav_container visible_phone">-->
	<!--			    <transition name="custom-classes-transition" enter-active-class="animated slideInDown" leave-active-class="animated slideOutUp">-->
	<!--					<nav id="mobile_nav" v-show="show_mobile_menu">-->
	<!--						<ul>-->
	<!--							<li v-for="item in menu_items" class="menu_item">-->
	<!--						        <router-link :to="item.href">{{$t(item.name)}}</router-link>-->
	<!--						        <ul v-if="item.sub_menu">-->
	<!--						            <li v-for="sub_menu in item.sub_menu" class="dropdown_item">-->
	<!--						                <router-link :to="sub_menu.href">{{$t(sub_menu.name)}}</router-link>-->
	<!--						            </li>-->
	<!--								</ul>-->
	<!--						    </li>-->
	<!--						</ul>-->
							
	<!--						<div class="header_social">-->
	<!--						<span v-for="item in social_media">-->
 <!--                               <a :href="item.url" target="_blank">-->
 <!--                                   <i :class="item.iconClass" aria-hidden="true"></i>-->
 <!--                               </a>-->
 <!--                           </span>-->
	<!--					</div>-->
						
							<!--<div class="small_hr"></div>-->
							<!--<div class="tel_num" v-if="property">-->
 <!--                               <a :href="'tel:'+property.contact_phone">{{property.contact_phone}}</a>-->
 <!--                           </div>-->
 <!--                           <div>-->
 <!--                              <p style="display:block"> {{property.address1}}</p>-->
 <!--                               <p style="display:block">{{property.city}}, {{property.postal_code}} {{property.province_state}}</p>-->
 <!--                           </div>-->
							<!--<div class="header_social">-->
							<!--	<a href="https://www.facebook.com/shopthegateway/" target="_blank"><img src="//codecloud.cdn.speedyrails.net/sites/59282acb6e6f647d8d520100/image/png/1495816064000/facebook_icon.png" class="header_social_icon" alt="Facebook Icon"></a>-->
							<!--	<a href="https://www.instagram.com/shopthegateway/" target="_blank"><img src="//codecloud.cdn.speedyrails.net/sites/59282acb6e6f647d8d520100/image/png/1495817456000/insta_icon.png" class="header_social_icon" alt="Instagram Icon"></a>-->
							<!--</div>-->
							<!--<div class="small_hr"></div>-->
	<!--					</nav>-->
	<!--				</transition>-->
	<!--			</div>-->
				<!--	</div>-->
				<!--</div>-->
	<!--		</div>-->
		<!--	<div class="menu_bar hidden_phone">-->
		<!--		<div class="site_container">-->
		<!--			<div class="nav_container hidden_phone">-->
		<!--				<div class="row top_nav hidden_phone">-->
		<!--					<nav id="primary_nav">-->
		<!--						<ul>-->
		<!--						    <li v-for="item in menu_items" class="menu_item">-->
		<!--						        <router-link :to="item.href">{{$t(item.name)}}</router-link>-->
		<!--						        <ul v-if="item.sub_menu">-->
		<!--						            <li v-for="sub_menu in item.sub_menu" class="dropdown_item">-->
		<!--						                <router-link :to="sub_menu.href">{{$t(sub_menu.name)}}</router-link>-->
		<!--						            </li>-->
		<!--								</ul>-->
		<!--						    </li>-->
		<!--						</ul>-->
		<!--					</nav>-->
		<!--				</div>-->
		<!--			</div>-->
		<!--		</div>-->
		<!--	</div>-->
		<!--</div>-->
  <!--  </header>-->
  
<script>
    define(["Vue", "vuex", "vue_router", "routes", "vue!today_hours.vue"], function (Vue, Vuex, VueRouter, appRoutes, TodayHoursComponent) {
        return Vue.component("header-component", {
            template: template, // the variable template will be injected,
            data: function () {
                return {
                    show_mobile_menu: false,
                    
                    // active: false, 
                    // newsletter_email: "",
                    // isOpen: false,
                    // windowWidth: 0,
                    // show_menu: true,
                    // showSubMenu1: false,
                    // showSubMenu2: false,
                    // showSubMenu3: false
                }
            },
            props:['menu_items', 'social_media'],
            watch: {
                $route: function() {
                    if (this.windowWidth <= 768) {
                        this.show_menu = false;
                    }  
                },
                windowWidth: function() {
                    if (this.windowWidth <= 768) {
                        this.show_menu = false;
                    } else {
                        this.show_menu = true;
                        document.body.classList.remove("no-scroll");
                    }
                },
                show_menu: function() {
                    if(this.show_menu == true){
                        document.body.classList.add("no-scroll");
                    } else if (this.show_menu == false) {
                        document.body.classList.remove("no-scroll");
                    }
                }
            },
            mounted() {
                this.$nextTick(function() {
                    window.addEventListener('resize', this.getWindowWidth);
                    //Init
                    this.getWindowWidth();
                });
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'hours',
                    'getTodayHours',
                ]),
                locale: {
                    get () {
                        return this.$store.state.locale
                    },
                    set (value) {
                        this.$store.commit('SET_LOCALE', { lang: value })
                    }
                },
                todays_hours() {
                    return this.getTodayHours;
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
                    this.showSubMenu1 = false;
                    this.showSubMenu2 = false;
                    this.showSubMenu3 = false;
                    
                    if(id == "dropDown1"){
                        this.showSubMenu1 = true   
                    } else if (id == "dropDown2"){
                        this.showSubMenu2 = true 
                    } else if (id == "dropDown3"){
                        this.showSubMenu3 = true 
                    }
                    
                }
            },
            beforeDestroy: function() {
                window.removeEventListener('resize', this.getWindowWidth);
            }
        });
    });
</script>