<template>
	<div class="Personal">
		<div class="Personal-header clearfix">
			<div @click="goback" class="Personal-header-return"><img src="../../../static/return.png">返回</div>
		</div>
		<div v-show="loading" class="loading">
			<img src="../../../static/img/loading.gif">
		</div>
		<div class="Personal-login">
			<div class="Personal-login-text">注册</div>
			
			<div class="Personal-name">
				<img src="../../../static/img/denglu.png"><input type="text" placeholder="请输入您的账号"  v-model="username" @click="pnone()">
			</div>
			<div class="Personal-name">
				<img src="../../../static/img/mima.png"><input type="password" placeholder="请输入您的密码"  v-model="password" @click="pnone()">
			</div>
			<div class="Personal-name">
				<input type="text" style="width: 50%;text-align: center;padding: 0;float: left;" placeholder="请输入验证码"/>
				<input type="button" style="width: 50%;text-align: center;padding: 0;float: left;" :value="value" @click="sendCode(this)" :disabled="disabled"/>
			</div>
			<p v-show="showTishi">{{tishi}}</p>
			<div class="Personal-login-button" @click="login">登录</div>
		</div>
	</div>
</template>

<style>
	.Personal{background:white;background-size:cover ;position: fixed;top: 0;bottom: 0;right: 0;left: 0;overflow-y: scroll;z-index: 10;padding-bottom: 1.1rem;}
	.Personal-header{height: 1rem;width: 100%;background:#5487d6;color: white;}
	.Personal-header-return{width:1.3rem ;height: 1rem;line-height: 1rem;font-size: 0.3rem;float: left;}
	.Personal-header-return img{width:0.2rem ;float: left;margin:0.33rem 0.1rem 0 0.2rem;}
	.Personal-header-center{width:5rem;text-align: center;line-height: 0.8rem;float: left;font-size: 0.36rem;}
	.Personal-login-text{font-size: 0.6rem;font-weight: bold;width: 6rem;margin: 0 auto;margin-bottom: 0.8rem;}
	.Personal-login{width: 100%;height: 6rem;position:absolute;margin-top: 1.5rem;}
	.Personal-login p{width: 100%;text-align: center;font-size: 0.28rem;line-height: 0.5rem;color: red;}
	.Personal-name{width: 6rem;margin: 0 auto;height: 1.2rem;position: relative;margin-bottom: 0.1rem;}
	.Personal-name img{position: absolute;height:0.6rem;top: 0.3rem;left: 0.05rem;}
	.Personal-name input{width: 100%;border: 0;outline: none;height: 100%;font-size: 0.3rem;box-sizing: border-box;padding-left: 0.8rem;border-bottom: 1px solid #DBDCDE;}
	.Personal-login-button{width: 6rem;background:#5487d6;color: white;font-size: 0.36rem;text-align: center;height: 0.9rem;line-height: 0.9rem;border-radius:0.1rem ;margin: 0.65rem auto 0rem auto;}
	.loading{position: absolute;width: 100%;height: 100%;background: white;text-align: center;z-index: 1;}
	.loading img{width:4rem;top: 1rem;position: absolute;left: 50%;top: 50%;margin-left: -25%;margin-top: -35%;}
</style>

<script>
	import axios from 'axios'
	import Vue from 'vue'
	import qs from 'qs';
	
	export default {
		data(){
            return{
            	loading:false,
                showLogin: true,
                showRegister: false,
                showTishi: false,
                tishi: '',
                username: '',
                password: '',
                newUsername: '',
                newPassword: '',
                userId:'',
                nums:10,
                clock:"",
                btn:"",
                disabled:false,
                value:"点击发送验证码"
            }
       },
		methods:{
				pnone(){
					let _this = this;
					_this.showTishi = false
				},
				
				goback(){
					window.history.back()
				},
				 sendCode(thisBtn){ 
				 	let _this = this
					 _this.btn = thisBtn;
					 _this.disabled = true; //将按钮置为不可点击
					 _this.value = _this.nums+'秒后可重新获取';
					 _this.clock = setInterval(
					 	function(){
					 		_this.nums--;
							 if(_this.nums > 0){
							  _this.value = _this.nums+'秒后可重新获取';
							 }else{
							  clearInterval(_this.clock); //清除js定时器
							  _this.disabled = false;
							  _this.value = '点击发送验证码';
							  _this.nums = 10; //重置时间
							 }
					 	}, 1000); //一秒执行一次
				 },
				 doLoop(){
				 _this.nums--;
					 if(_this.nums > 0){
					  _this.value = n_this.ums+'秒后可重新获取';
					 }else{
					  clearInterval(_this.clock); //清除js定时器
					  _this.disabled = false;
					  _this.value = '点击发送验证码';
					  _this.nums = 10; //重置时间
					 }
				 },
				login(){
					let _this = this
			        if(_this.username == "" || _this.password == ""){
			            
			            _this.tishi = "请输入用户名或密码"
			            _this.showTishi = true
			        }else{
			            /*接口请求*/
			           this.$post('site/login',qs.stringify({username:_this.username,password:_this.password}))
					      .then((response) => {
					        if(response.data.status == "erro"){
				                  _this.tishi = "账号或密码错误"
				                  _this.showTishi = true
				              }
				              else{
				              	  _this.userId = JSON.parse(response.data.data).userId
				              	  console.log(_this.userId)
				                  _this.$cookie.set('username',_this.username,1)
				        	  	_this.$cookie.set('userId',_this.userId,1)
				                  _this.loading = true
				                  setTimeout(function(){
				                      window.history.back()
				                  }.bind(_this),1000)
				                  
				              }
				              
					      })
					    
			           
			            
			        }
			      }
			    
		},
		mounted(){
		  /*页面挂载获取cookie，如果存在username的cookie，则跳转到主页，不需登录*/ 
		    if(this.$cookie.get('username')){
		        this.$router.push('/')
		    }
		    
		  },
		watch:{
	         '$route' (to, from) { 
			 if (to.name === 'Personal') {//跳转的的页面的名称是"Personal"
				if(this.$cookie.get('username')==""){
			    	let _this = this
			    	_this.loading = false
			    }
				 } 
	    	}
	    }
	}
</script>
