<template>
    <div class="modal-body">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="alert alert-danger" v-if="authenticationError" v-html="$t('login.messages.error.authentication')">
                    <strong>Failed to sign in!</strong> Please check your credentials and try again.
                </div>
            </div>
            <div class="col-md-8">
                <form class="form" role="form" v-on:submit.prevent="login()">
                    <div class="form-group">
                        <label for="_username" v-text="$t('global.form.username')">Login</label>
                        <input type="text" class="form-control" name="username" id="_username" v-bind:placeholder="$t('global.form.username-placeholder')"
                               v-model="username">
                    </div>
                    <div class="form-group">
                        <label for="_password" v-text="$t('login.form.password')">Password</label>
                        <input type="password" class="form-control" name="password" id="_password" v-bind:placeholder="$t('login.form.password-placeholder')"
                               v-model="password">
                    </div>
                    <div class="form-check">
                        <label class="form-check-label" for="_rememberMe">
                            <input class="form-check-input" type="checkbox" name="rememberMe" id="_rememberMe" v-model="rememberMe" checked>
                            <span v-text="$t('login.form.rememberme')">Remember me</span>
                        </label>
                    </div>
                    <button type="submit" class="btn btn-primary" v-text="$t('login.form.button')">Sign in</button>
                </form>
                <p></p>
                <div class="alert alert-warning">
                    <router-link class="alert-link" to="/resetPassword" v-text="$t('login.password.forgot')">Did you forget your password?</router-link>
                </div>
                <div class="alert alert-warning">
                    <span v-text="$t('global.messages.info.register.noaccount')">You don't have an account yet?</span>
                    <router-link class="alert-link" to="/register" v-text="$t('global.messages.info.register.link')">Register a new account</router-link>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    import axios from 'axios'
    import {SERVER_API_URL} from "../../constants";
    import Principal from './Principal';

    export default {
        mixins: [Principal],
        data() {
          return {
              authenticationError: null,
              username: null,
              password: null,
              rememberMe: null
          }
        },
        methods: {
            login: function () {
                let vm = this;
                const data =
                    'j_username=' +
                    encodeURIComponent(this.username) +
                    '&j_password=' +
                    encodeURIComponent(this.password) +
                    '&remember-me=' +
                    this.rememberMe +
                    '&submit=Login';
                axios.post(SERVER_API_URL + 'api/authentication', data,{
                    headers: {
                        'Content-Type': 'application/x-www-form-urlencoded'
                    }
                }).then(function () {
                    vm.authenticationError = false;
                    vm.$root.$emit('bv::hide::modal','loginModal');
                    vm.retrieveAccount();
                }).catch(()=> {
                    this.authenticationError = true;
                });
            }
        },
        watch:{
            $route (){
                this.$root.$emit('bv::hide::modal','loginModal');
            }
        }
    }
</script>
