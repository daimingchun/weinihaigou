<template>
<div>
    <div class="login-header">
        <router-link to="index" class="header-back-white"></router-link>
        <img src="../m-images/login-bg.jpg" />
   </div>
   <div class="login-main" v-cloak>
        <div class="phone-pwd">
            <div class="phone">
                <input type="tel" placeholder="请输入账号" autocomplete="off" v-model="mobile.value"/>
            </div>
            <div class="pwd">
                <input type="password" placeholder="请输入密码" autocomplete="off" v-model="password.value"/>
            </div>
        </div>
        <a href="javascript:;" class="com-next" v-on:click="login()" :class="{unable:(mobile.value=='' || password.value=='')}">登录</a>
        <p class="register-back">
            <router-link to="register" class="com-color">注册账号</router-link>
        </p>
   </div>
</div>
</template>

<script>
import axios from 'axios'
import common from '../js/common'
import hex_md5 from '../js/md5'
import API from '../js/api.js'
export default {

    name : 'search',

    data () {
        return {
            mobile : {value:'', success:false, errorInfo: '手机号为空或者格式不正确'},
            password: {value:'', success:false, errorInfo: '密码为空或者格式不正确（长度在6-20位之间）'},
        }
    },

    watch : {},

    methods : {

        checkMobile: function() {
            var oThis = this;
            if( common.testPhone(this.mobile.value) ) {
                this.mobile.success = true;
            } else {
                this.mobile.success = false;
                $.customTips({
                    msg : this.mobile.errorInfo
                });
            }
        },

        checkPassword: function() {
            if( common.testPwd(this.password.value) ) {
                this.password.success = true;
            } else {
                this.password.success = false;
                $.customTips({
                    msg : this.password.errorInfo
                });
            }
        },

        login : function() {
            var oThis = this;
            if ( this.mobile.value=="" || this.password.value=="" ) {
                return;
            }
            this.checkMobile();
            if( this.mobile.success ) {
                this.checkPassword();
                if( this.password.success ) {
                    $.ajax({
                        type : 'post',
                        url:  API.login,
                        data: {
                            mobile : oThis.mobile.value,
                            password : hex_md5(oThis.password.value),
                        },
                        dataType: 'json',
                        success : function(data) {
                            console.log(data);
                            var msg = data.msg;
                            if( msg=='00000000' ) {
                                $.customTips({
                                    msg : '登录成功,正在拼命跳转中...'
                                });
                                oThis.$store.commit('checkUser');
                                oThis.$router.push('/index');
                            } else if( msg=='00000001' ) {
                                $.customTips({
                                    msg : '用户名或者密码错误'
                                });
                            } else if( msg=='00000007' ) {
                                $.customTips({
                                    msg : '手机号错误'
                                });
                            }
                        }
                    });
                }
            }
        }
    },

    components : {
    }
}
</script>

<style scoped>
.login-header {
    position:relative;
}
.login-main {
    padding: 1.333rem;
}
.login-main .phone-pwd {
    border: 1px solid #b5b5b5;
    padding: 15px;
    background-color: #fff;
}
.login-main  .phone {
    height: 2.5rem;
    border-bottom: 1px solid #eee;
    padding-left: 1.875rem;
    background:url(../m-images/icon.png) 0 0.308rem no-repeat;
    background-size: 18.875rem 14.167rem;
}
.login-main .phone input,
.login-main .pwd input {
    width: 20rem;
    height: 2rem;
    border:none;
    font-size: 1.2rem;
}
.login-main .phone-pwd .pwd {
    height: 1.8rem;
    padding-left: 1.875rem;
    padding-top: 0.833rem;
    border-bottom: none;
    background:url(../m-images/icon.png) 0 -3.5rem no-repeat;
    background-size: 18.875rem 14.167rem;
    box-sizing:content-box;
}
.login-main .register-back {
    display: -webkit-flex;
    display: flex;
    -webkit-justify-content: space-between;
    justify-content: space-between;
    margin-top: 10px;
    color: #000;
}
.login-main .register-back a {
    color: #000;
}
.login-main .register-back nth-child(1) {
    color: #9f2e33;
}
.login-main .unable {
    background: #d2d2d2;
}
</style>

