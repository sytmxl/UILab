<template>
 <div id="app">
   <router-view v-if="isRouterAlive"/>

 </div>
 
</template>
 
<script>
import ProjectAside from "@/components/ProjectAside";
export default {
 name: 'App',
  components: {ProjectAside},
  provide () {
   return{
     reload: this.reload
   }
 },
 data() {
   return {
       isRouterAlive: true,
     editing : sessionStorage.getItem('editing')
   }
 },
 methods:{
   reload(){
     this.isRouterAlive = false
     this.$nextTick(function(){
       this.isRouterAlive = true
     })
   }
 }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: gray;
}
* {
  margin: 0;
  padding: 0;
    animation-name: enter_blur;
    animation-iteration-count: 1;
    animation-duration: 0.4s;
}

/* 弹窗类效果
.el-dialog__wrapper {
	transition-duration: 0.3s;
}
.dialog-fade-enter-active{
	animation: none !important;
}
.dialog-fade-leave-active {
	transition-duration: 0.15s !important;
	animation: none !important;
}
.dialog-fade-enter-active .el-dialog,
.dialog-fade-leave-active .el-dialog{
	animation-fill-mode: forwards;
}
.dialog-fade-enter-active .el-dialog{
	animation-duration: 0.3s;
	animation-name: anim-open;
	animation-timing-function: cubic-bezier(0.6,0,0.4,1);
}
.dialog-fade-leave-active .el-dialog{
	animation-duration: 0.3s;
	animation-name: anim-close;
}

.el-message-box__wrapper {
	transition-duration: 0.3s;
}
.message-box-fade-enter-active{
	animation: none !important;
}
.message-box-fade-leave-active {
	transition-duration: 0.15s !important;
	animation: none !important;
}
.message-box-fade-enter-active .el-message-box,
.message-box-fade-leave-active .el-message-box{
	animation-fill-mode: forwards;
}
.message-box-fade-enter-active .el-message-box{
	animation-duration: 0.3s;
	animation-name: anim-open;
	animation-timing-function: cubic-bezier(0.6,0,0.4,1);
}
.message-box-fade-leave-active .el-message-box{
	animation-duration: 0.3s;
	animation-name: anim-close;
}
@keyframes anim-open {
	0% { opacity: 0;  transform: scale3d(0, 0, 1); }
	100% { opacity: 1; transform: scale3d(1, 1, 1); }
}
@keyframes anim-close {
	0% { opacity: 1; }
	100% { opacity: 0; transform: scale3d(0.5, 0.5, 1); }
} */


el-icon:hover, el-icon-close:hover {
    color:  #748391 !important;
}
.el-input__inner,.el-textarea__inner {
  border-radius: 10px !important;
}
.el-input__inner:focus,.el-textarea__inner:focus {
  border-color: #748391 !important;
  /* background-color: #edeff071 !important; */
  /* background-color: #c8d3dd !important; */
}
.el-dialog, .el-message-box {
  width: 80%;
  padding: 10px 10px 25px 10px !important;
  border-radius: 20px !important;
  border-color: #9aadbd !important;
  /* backdrop-filter: blur(25px) brightness(110%); */
  background-color: #f4f5f6 !important; 
  /* transition: 1s !important; */
  /* -n-bezier-ease-out: cubic-bezier(0, 0, 0.2, 1); */
  font-family: 微软雅黑;
}
.el-dialog__wrapper, .el-message-box__wrapper {
  /* backdrop-filter: blur(15px) brightness(150%) !important;   */
  /* transition: 1s !important; */
}
.el-button {
  border-radius: 10px !important;
  transition: 0.4s !important;  
}
.el-button--primary {
  background-color: #9aadbd !important;
  border: none !important;
}
.el-button--primary:hover {
  background-color: #c8d3dd !important;
  color: #ffffff !important;
  border-color: #c8d3dd !important;
}
.el-button--default:hover {
  /* width: 20%; */
  background-color: #c8d3dd !important;
  color: #ffffff !important;
  border-color: #c8d3dd !important;
}
.el-popper, .el-dropdown-menu__item {
  color: #35424e !important;
  font-family: 微软雅黑 !important;
}
.el-dropdown-menu__item:hover {
  color: #35424e !important;
  background: rgba(191, 202, 217, 0.495) !important;
}

::-webkit-scrollbar {
  width: 10px;
  height: 10px;
}

::-webkit-scrollbar-track {
  width: 6px;
  background: rgba(#101F1C, 0.1);
  -webkit-border-radius: 2em;
  -moz-border-radius: 2em;
  border-radius: 2em;
}

::-webkit-scrollbar-thumb {
  background-color: rgba(144,147,153,.5);
  background-clip: padding-box;
  min-height: 28px;
  -webkit-border-radius: 2em;
  -moz-border-radius: 2em;
  border-radius: 2em;
  transition: background-color .3s;
  cursor: pointer;
}

::-webkit-scrollbar-thumb:hover {
  background-color: rgba(144,147,153,.3);
}

.el-tooltip:focus, .el-tooltip:active, .el-tooltip, .el-tooltip:hover {
  color: #b0c3d1 !important;
}
.contextmenu_menu {
  border-radius: 15px !important;
  font-family: 微软雅黑 !important;
}
.contextmenu_menu_item:hover, .contextmenu_menu_item:focus, .contextmenu_menu_item:active{
  color: #35424e !important;
  background: rgba(191, 202, 217, 0.495) !important;
}
 /deep/ .contextmenu_menu_item:visited {
  color: #35424e !important;
  background: rgba(191, 202, 217, 0.495) !important;
}
.el-tooltip:focus, .el-tooltip:active, .el-tooltip, .el-tooltip:hover {
  color: #b0c3d1 !important;
}
iframe {
  border: none;
}

/* 右键 */
/* custom */
.custom-class .menu_item__available:hover,
.custom-class .menu_item_expand {
  background: #ebebef !important;
  color: #404042 !important;
}
/* antd */
.antd-theme.menu {
  border-radius: 2px !important;
}
.antd-theme .menu_item {
  color: #000000d9 !important;
}
.antd-theme .menu_item__available:hover {
  background: #f5f5f5 !important;
}
.antd-theme .menu_item_expand {
  font-weight: 600 !important;
  background-color: #9badb6 !important;
}
/* material */
.material-theme.menu {
  box-shadow: 0px 5px 5px -3px rgba(0, 0, 0, 0.2),
    0px 8px 10px 1px rgba(0, 0, 0, 0.14), 0px 3px 14px 2px rgba(0, 0, 0, 0.12) !important;
}
.material-theme .menu_item {
  color: #000000de !important;
}
.material-theme .menu_item__available:hover,
.material-theme .menu_item_expand {
  background: rgba(0, 0, 0, 0.04) !important;
}

.el-input {
  width: 90%;
}


/* 动画库 */
@keyframes enter_label {
  0% {
    margin-left: 0px;
    filter: blur(20px);
    opacity: 0%;
  }
  100% {
    margin-left: 50px;
    filter: blur(0px);
    opacity: 100%;
  }
}
@keyframes enter_aside {
  0% {
    left:-80px;
    filter: blur(20px);
    opacity: 0%;
  }
  100% {
    left:0px;
    filter: blur(0px);
    opacity: 100%;
  }
}
@keyframes enter_blur {
  0% {
    filter: blur(20px);
    opacity: 0%;
  }
  100% {
    filter: blur(0px);
    opacity: 100%;
  }

}
</style>

