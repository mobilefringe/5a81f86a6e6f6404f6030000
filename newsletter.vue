<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loader v-if="!dataLoaded"></loader>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak>
        		<div class="site_container">
                    <div class="contact_container">
                        <div class="row">
                            <div class="col-md-4">
                                
                            </div>
                            <div class="col-md-8">
                                <form class="form-horizontal" action="https://mobilefringe.createsend.com/t/d/s/bidirr/" method="post" @submit.prevent="validateBeforeSubmit">
                                    <div class="form-group ">
                                        <div class="col-sm-6 col-xs-12" >
                                            <label class="label" for="cm-name">{{$t("newsletter_page.name")}}</label>
                                            <input v-model="form_data.name" required class="form-control" name="cm-name" type="text" placeholder="Name">
                                        </div>
                                    </div>
                                    <div class="form-group">
                                        <div class="col-sm-6 col-xs-12">
                                            <label class="label" for="cm-irudui-irudui">{{$t("newsletter_page.email")}}</label>
                                            <input v-model="form_data.email" required class="form-control" name="cm-bidirr-bidirr" type="email" placeholder="Email" id="newsletter_email">
                                        </div>
                                    </div>
                                    <div class="form-group">
                                        <div class="col-xs-12">
                					        <label class="checkbox">
                                                <input name="agree_newsletter" required  type="checkbox">
                                                    {{$t("newsletter_page.agree")}} {{property.name}}. 
                                            </label>
                					    </div>
                					</div>
                					<div class="form-group">
                                        <div class="col-xs-12">
                                            <button class="contest_btn" type="submit" :disabled="formSuccess">{{$t("newsletter_page.subscribe")}}</button>
                                        </div>
                                    </div>
                                </form>
                            
                                <div id="send_contact_success" class="alert alert-success" role="alert" v-show="formSuccess">
                                    <span class="glyphicon glyphicon-ok" aria-hidden="true"></span>
                                    <span class="sr-only">{{$t("newsletter_page.success")}} : </span>
                                    {{$t("newsletter_page.thank_you_message")}}
                                </div>
                                <div id="send_contact_error" class="alert alert-danger" role="alert" v-show="formError">
                                    <span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
                                    <span class="sr-only">{{$t("newsletter_page.error")}} : </span>
                                    {{$t("newsletter_page.error_message")}}
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </transition>
    </div>
</template>

<script>
    define(["Vue", "vuex", "vue-meta", "vee-validate", "jquery", "utility"], function(Vue, Vuex, Meta, VeeValidate, $, Utility) {
        Vue.use(Meta);
        Vue.use(VeeValidate);
        return Vue.component("newsletter-component", {
            template: template, // the variable template will be injected
            data: function() {
                return {
                    dataLoaded: false,
                    currentPage: null,
                    form_data : {},
                    formSuccess : false,
                    formError: false
                }
            },
            created () {
                this.loadData().then(response => {
                   this.dataLoaded = true;
                });    
            },
            mounted () {
                this.form_data.email = this.$route.query.email;
                $("#newsletter_email").val(this.form_data.email);
            },
            watch : {
                $route () {
                    this.form_data.email = this.$route.query.email;
                    $("#newsletter_email").val(this.form_data.email);
                }
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone'
                ])
            },
            methods: {
                validateBeforeSubmit(form) {
                    this.$validator.validateAll().then((result) => {
                        if (result) {
                            let errors = this.errors;
                            
                            if(errors.length > 0) {
                                console.log("Error");
                                this.formError = true;
                            } else {
                                form.preventDefault();
                                console.log("No Error", form);
                                var vm = this;
                                $.getJSON(
                                form.target.action + "?callback=?",
                                $(form.target).serialize(),
                                function (data) {
                                    if (data.Status === 400) {
                                       vm.formError = true;
                                    } else { // 200
                                        vm.formSuccess = true;
                                    }
                                });
                            }
                        }
                    })
                },
                loadData: async function() {
                    try {
                        // avoid making LOAD_META_DATA call for now as it will cause the entire Promise.all to fail since no meta data is set up.
                        let results = await Promise.all([,this.$store.dispatch("getData", "repos")]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
            }
        });
    });
</script>
