<template>
    <footer>
        <div class="newsletter_subscription">
            <div class="newsletter_content_container">
                 <h4 class="caps">{{$t("footer.stay_uptodate")}}</h4> 
                 <div id="newsletter_form">
                    <label for="fieldEmail" style="display:none"></label>
                    <input id="fieldEmail" name="cm-tkyhii-tkyhii" class="form-control" type="email" v-model="newsletter_email" :placeholder="$t('footer.enter_email')" required/> 
                    <router-link class="newsletter_btn" data-i18n="general.submit" :to="'/newsletter?email='+ newsletter_email"><i class="fa fa-arrow-right"></i></router-link>
                    
                    <span id="success_subscribe" class="hidden_now">{{$t("footer.subscribe_thankyou")}}</span>
                 </div>
            </div>
        </div>
		<div class="footer">
			<div class="site_container">
			    <div class="row footer_logo">
			        <div class="col-md-4">
			            <div class="property_logo">
					        <router-link to="/">
					            <img src="//codecloud.cdn.speedyrails.net/sites/5a81f86a6e6f6404f6030000/image/png/1519154972000/mm_logo.png" alt="Property Logo"/>
				            </router-link>
				        </div>    
			        </div>
			        <div class="col-md-4">
			            <nav id="primary_nav">
    						<ul>
    						    <li v-for="item in footer_menu_items" class="menu_item">
    						        <router-link :to="item.href">{{$t(item.name)}}</router-link>
    						    </li>
    						</ul>
    					</nav>    
			        </div>
			        <div class="col-md-4">
			            <div class="social_icons">
                            <span v-for="item in social_media">
                                <a :href="item.url" target="_blank">
                                    <i :class="item.iconClass" aria-hidden="true"></i>
                                </a>
                            </span>
                        </div>    
			        </div>
			    </div>
				<div class="footer_terms">
					<p>&copy; {{copyright_year}}<span class="caps"> {{property.name}}</span> {{$t("footer.all_rights")}}<router-link to="/pages/bonniedoon-privacy-policy"> {{$t("footer.privacy_policy")}} </router-link> <span class="hidden_phone">|</span><br class="visible_phone"/><span> {{$t("footer.powered_by")}}<a href="//www.mallmaverick.com" target="_blank"> Mall Maverick</a></span></p>
				</div>
			</div>
		</div>
    </footer>
</template>

<script>
    define(["Vue", "vuex", "moment", "moment-timezone", "vue-moment", "vue!search-component"], function (Vue, Vuex, moment, tz, VueMoment, SearchComponent) {
        return Vue.component("footer-component", {
            template: template, // the variable template will be injected,
            data: function data() {
                return {
                    newsletter_email: "",
                    
                    // suggestionAttribute: 'name',
                    // search: '',    
                }
            },
            props:['footer_menu_items', 'social_media'],
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'processedStores'
                ]),
                copyright_year() {
                    return moment().year();
                }
            },
            methods: {
                onOptionSelect(option) {
                    console.log('Selected option:', option);
                    this.$nextTick(function() {
                        this.search = ""
                    });
                    this.$router.push("/stores/" + option.slug);
                }
            }
        });
    });
</script>