<template>
    <div>
        <div class="row justify-content-center">
            <div class="col-md-8">
                <h1 v-text="$t('reset.request.title')">Reset your password</h1>

                <div class="alert alert-danger" v-html="$t('reset.request.messages.notfound')" v-if="errorEmailNotExists">
                    <strong>Email address isn't registered!</strong> Please check and try again.
                </div>

                <div class="alert alert-warning" v-if="!success">
                    <p v-text="$t('reset.request.messages.info')">Enter the email address you used to register.</p>
                </div>

                <div class="alert alert-success" v-if="success === 'OK'">
                    <p v-text="$t('reset.request.messages.success')">Check your emails for details on how to reset your password.</p>
                </div>

                <form v-if="!success" name="form" role="form" v-on:submit.prevent="requestReset()">
                    <div class="form-group">
                        <label class="form-control-label" for="email" v-text="$t('global.form.email')">Email</label>
                        <input type="email" class="form-control" id="email" name="email" v-bind:placeholder="$t('global.form.email-placeholder')"
                               :class="{'valid': !$v.resetAccount.email.$invalid, 'invalid': $v.resetAccount.email.$invalid }"
                               v-model="$v.resetAccount.email.$model" minlength=5 maxlength=254  email required>
                        <div v-if="$v.resetAccount.email.$anyDirty && $v.resetAccount.email.$invalid">
                            <small class="form-text text-danger" v-if="!$v.resetAccount.email.required"
                                   v-text="$t('global.messages.validate.email.required')">
                                Your email is required.
                            </small>
                            <small class="form-text text-danger" v-if="!$v.resetAccount.email.email"
                                   v-text="$t('global.messages.validate.email.invalid')">
                                Your email is invalid.
                            </small>
                            <small class="form-text text-danger" v-if="!$v.resetAccount.email.minLength"
                                   v-text="$t('global.messages.validate.email.minlength')">
                                Your email is required to be at least 5 characters.
                            </small>
                            <small class="form-text text-danger" v-if="!$v.resetAccount.email.maxLength"
                                   v-text="$t('global.messages.validate.email.maxlength')">
                                Your email cannot be longer than 100 characters.
                            </small>
                        </div>
                    </div>
                    <button type="submit" :disabled="$v.resetAccount.$invalid" class="btn btn-primary" v-text="$t('reset.request.form.button')">Reset</button>
                </form>
            </div>
        </div>
    </div>
</template>
<script>
    import {email, maxLength, minLength, required} from 'vuelidate/lib/validators'
    import axios from 'axios'
    import {EMAIL_NOT_FOUND_TYPE, SERVER_API_URL} from "../../constants";

    export default {
        data() {
            return {
                errorEmailNotExists: null,
                success: null,
                resetAccount: {
                    email: null
                }
            }
        },
        validations: {
            resetAccount: {
                email: {
                    required,
                    minLength: minLength(5),
                    maxLength: maxLength(254),
                    email
                }
            }
        },
        methods: {
            requestReset: function () {
                let vm = this;
                axios.post(SERVER_API_URL + 'api/account/reset-password/init', this.resetAccount.email).then(() => {
                    vm.success = true;
                }).catch(error => {
                    this.success = null;
                    if (error.response.status === 400 && error.response.data.type === EMAIL_NOT_FOUND_TYPE) {
                        this.errorEmailNotExists = 'ERROR';
                    } else {
                        this.error = 'ERROR';
                    }
                });
            }
        }
    }
</script>
