<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loader v-if="!dataLoaded"></loader>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak>
        		<div class="site_container">
        		    <div class="margin_60 hidden_phone"></div>
                    <div>
        		        <h3 class="home_page_title caps">{{$t("home_page.twitter_feed")}}</h3>
        		    </div>
        		    <twitter-slider></twitter-slider>
                </div>
            </div>
        </transition>
    </div>
</template>

<script>
    define(["Vue", "vuex", "vue-meta", "vee-validate", "utility"], function(Vue, Vuex, Meta, VeeValidate, Utility) {
        Vue.use(Meta);
        Vue.use(VeeValidate);
        return Vue.component("contact-us-component", {
            template: template, // the variable template will be injected
            data: function() {
                return {
                    dataLoaded: false,
                    form_data : {},
                    formSuccess : false,
                    formError: false
                }
            },
            created(){
                this.loadData().then(response => {
                   this.dataLoaded = true;
                   console.log(this.property)
                });
            },
            mounted () {
                //creating random validation num 
                // this.correctValNum = Utility.rannumber();//this.rannumber;
                //ensuring the variables are created in this order for email
                this.form_data.name = null;
                this.form_data.email = null;
                this.form_data.phone = null;
                this.form_data.subject = this.property.name + ' Contact Us Form';
                this.form_data.message = null;
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone'
                ]),
                getPropertyAddress() {
                    return this.property.name + ' ' + this.property.address1 + ' ' + this.property.city + ' ' + this.property.country + ' ' +this.property.province_state + ' ' + this.property.province_state
                }
            },
            methods: {
                validateBeforeSubmit() {
                    this.$validator.validateAll().then((result) => {
                        this.validNumError = false;
                        let errors = this.errors;
                        send_data = {};
                        send_data.form_data = JSON.stringify(Utility.serializeObject(this.form_data));
                        this.$store.dispatch("CONTACT_US", send_data).then(res => {
                            this.formSuccess = true;
                        }).catch(error => {
                            try {
                                if (error.response.status == 401) {
                                    console.log("Data load error: " + error.message);
                                    this.formError = true;
                                } else {
                                    console.log("Data load error: " + error.message);
                                    this.formError = true;
                                }
                            } catch (e) {
                                console.log("Data load error: " + error.message);
                                this.formError = true;
                            }
                        });
                    });
                },
                loadData: async function() {
                    try {
                        // avoid making LOAD_META_DATA call for now as it will cause the entire Promise.all to fail since no meta data is set up.
                        let results = await Promise.all([this.$store.dispatch("getData", "repos")]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
            }
        });
    });
</script>
