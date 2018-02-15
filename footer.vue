<template>
    <footer>
        <div class="home_subscription hidden_phone">
            <div class="subscribeBlog content_container">
                 <h4 class="caps">{{$t("footer.stay_uptodate")}}</h4> 
                 <div id="newsletterBlogForm">
                    <label for="fieldEmail" style="display:none"></label>
                    <input id="fieldEmail" name="cm-tkyhii-tkyhii" class="form-control" type="email" v-model="newsletter_email" :placeholder="$t('footer.enter_email')" required/> 
                    <router-link class="contact_btn" data-i18n="general.submit" :to="'/newsletter?email='+ newsletter_email"><i class="fa fa-arrow-right"></i></router-link>
                    
                    <span id="success_subscribe" class="hidden_now">{{$t("footer.subscribe_thankyou")}}</span>
                 </div>
            </div>
        </div>
		<div class="footer">
			<div class="site_container">
				<div class="site_logo visible_phone">
					<router-link to="/"><img src="http://via.placeholder.com/500x150/fff" alt="Property Logo"/></router-link>
				</div>
				<div class="footer_nav hidden_phone">
				    <nav id="primary_nav">
						<ul>
						    <li v-for="item in menu_items" class="menu_item">
						        <router-link :to="item.href">{{$t(item.name)}}</router-link>
						    </li>
						</ul>
					</nav>
			    </div>
				<div class="footer_social">
					<a href="https://www.instagram.com/shopbdc_" target="_blank"><i class="fa fa-instagram" aria-hidden="true"></i></a>
                    <a href="https://www.facebook.com/bonniedoonsc" target="_blank"><i class="fa fa-facebook-official" aria-hidden="true"></i></a>
                    <a href="https://www.pinterest.ca/bonniedoonsc" target="_blank"><i class="fa fa-pinterest" aria-hidden="true"></i></a>
                    <a href="https://www.twitter.com/bonniedoonsc" target="_blank"><i class="fa fa-twitter" aria-hidden="true"></i></a>
				</div>
				<div class="footer_terms">
					<p>&copy; {{copyright_year}}<span class="caps"> {{property.name}}</span> {{$t("footer.all_rights")}}<router-link to="/pages/bonniedoon-privacy-policy"> {{$t("footer.privacy_policy")}} </router-link> <span class="hidden_phone">|</span><br class="visible_phone"/><span> {{$t("footer.powered_by")}}<a href="//www.mallmaverick.com" target="_blank"> Mall Maverick</a></span></p>
					<!--<p>Powered by <a href="//www.mallmaverick.com" target="_blank">Mall Maverick</a></p>-->
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
            props:['social_media'],
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