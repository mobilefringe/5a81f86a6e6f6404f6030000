<template>
    <header>
        <div class="sticky"><!--<div :class="{sticky : stickyMenu}">-->
			<div class="top_bar">
				<div class="site_container">
					<div class="row top_bar_wrapper">
						<div class="col-sm-6">
							<div class="mobile_site_logo visible_phone">
							    <router-link to="/"><img src="http://via.placeholder.com/500x150/fff" alt="Property Logo"/></router-link>
						    </div>
							<div id="home_hours_container" class="hidden_phone">
								<p class="open_now"><span v-if="!todays_hours.is_closed || todays_hours.is_closed == null">{{$t("header.open_today")}}</span><span v-else>{{$t("header.closed_today")}}</span> <span style="margin:0 20px">|</span> {{todays_hours.open_time | moment("h:mma", timezone)}} - {{todays_hours.close_time | moment("h:mma", timezone)}}</p>
							</div>
						</div>
						<div class="col-sm-6 hidden_phone text-right">
							<div class="header_social">
								<a href="https://www.instagram.com/shopbdc_" target="_blank"><i class="fa fa-instagram" aria-hidden="true"></i></a>
                                <a href="https://www.facebook.com/bonniedoonsc" target="_blank"><i class="fa fa-facebook-official" aria-hidden="true"></i></a>
                                <a href="https://www.pinterest.ca/bonniedoonsc" target="_blank"><i class="fa fa-pinterest" aria-hidden="true"></i></a>
                                <a href="https://www.twitter.com/bonniedoonsc" target="_blank"><i class="fa fa-twitter" aria-hidden="true"></i></a>
								
								<!--<a href="#" data-toggle="modal" data-target="#snap_modal"><div class="social_snapchat"></div></a>-->
								<!--<a href=" https://open.spotify.com/user/playlist/6ozU5Na6bFInmZOVTnSYIm" target="_blank"><div class="social_spotify"></div></a>-->
							</div>
							<router-link id="signup" to="/newsletter">{{$t("header.sign_up")}}</router-link>
							<span> <span @click="changeLocale('en-ca')"> en</span> | <span @click="changeLocale('fr-ca')">fr</span></span>
						</div>
						<div id="menu-icon" @click="show_mobile_menu = !show_mobile_menu" :class="{ open: show_mobile_menu}">
							<span></span>
							<span></span>
							<span></span>
							<span></span>
						</div>
						<div class="mobile_nav_container visible_phone">
						    <transition name="custom-classes-transition" enter-active-class="animated slideInDown" leave-active-class="animated slideOutUp">
								<nav id="mobile_nav" v-show="show_mobile_menu">
									<ul>
										<div class="mobile_menu_site_logo">
											<router-link to="/"><img src="http://via.placeholder.com/500x150/000" alt="Property Logo"/></router-link>
										</div>
										<li v-for="item in menu_items" class="menu_item">
    								        <router-link :to="item.href">{{$t(item.name)}}</router-link>
    								        <ul v-if="item.sub_menu">
    								            <li v-for="sub_menu in item.sub_menu" class="dropdown_item">
    								                <router-link :to="sub_menu.href">{{$t(sub_menu.name)}}</router-link>
    								            </li>
    										</ul>
    								    </li>
									</ul>
									<div class="small_hr"></div>
									<div class="tel_num" v-if="property">
                                        <a :href="'tel:'+property.contact_phone">{{property.contact_phone}}</a>
                                    </div>
                                    <div>
                                       <p style="display:block"> {{property.address1}}</p>
                                        <p style="display:block">{{property.city}}, {{property.postal_code}} {{property.province_state}}</p>
                                    </div>
									<div class="header_social">
										<a href="https://www.facebook.com/shopthegateway/" target="_blank"><img src="//codecloud.cdn.speedyrails.net/sites/59282acb6e6f647d8d520100/image/png/1495816064000/facebook_icon.png" class="header_social_icon" alt="Facebook Icon"></a>
										<a href="https://www.instagram.com/shopthegateway/" target="_blank"><img src="//codecloud.cdn.speedyrails.net/sites/59282acb6e6f647d8d520100/image/png/1495817456000/insta_icon.png" class="header_social_icon" alt="Instagram Icon"></a>
									</div>
									<div class="small_hr"></div>
								</nav>
							</transition>
						</div>
					</div>
				</div>
			</div>
			<div class="menu_bar hidden_phone">
				<div class="site_container">
					<div class="nav_container hidden_phone">
						<div class="site_logo">
							<router-link to="/"><img src="http://via.placeholder.com/500x150/fff" alt="Property Logo"/></router-link>
						</div>
						<div class="row top_nav hidden_phone">
							<nav id="primary_nav">
								<ul>
								    <li v-for="item in menu_items" class="menu_item">
								        <router-link :to="item.href">{{$t(item.name)}}</router-link>
								        <ul v-if="item.sub_menu">
								            <li v-for="sub_menu in item.sub_menu" class="dropdown_item">
								                <router-link :to="sub_menu.href">{{$t(sub_menu.name)}}</router-link>
								            </li>
										</ul>
								    </li>
								</ul>
							</nav>
						</div>
					</div>
				</div>
			</div>
		</div>
    </header>
</template>

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