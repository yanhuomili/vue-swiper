<template>
	<div class="m-swiper">
		<div class="m-swiper-wrap" :style="{'width':wrapW}" ref="tab">
			<slot></slot>
		</div>
		<div v-if="showDisc" class="m-disc">
			<ul>
				<li v-for="dis in 5">{{dis}}</li>
			</ul>
		</div>
	</div>
</template>

<script>
	import MSwiperItem from './mSwiperItem'
  export default {
  	name:'mSwiper',
    data() {
      return {
      	index:0,
      	nowDis:0,
      	startX:0,
      	endX:0,
      	moveBox:null,
      	len:0,
      	wrapW:'',
      	parentW:'',
      	aa:'66666'
      }
    },
    mounted() {
    	var defaultW=document.documentElement.clientWidth;
    	this.moveBox=this.$refs.tab;
    	this.moveBox.addEventListener('touchstart',this.start);
    	this.moveBox.addEventListener('touchmove',this.move);
    	this.moveBox.addEventListener('touchend',this.end);
    	this.len=this.moveBox.children.length;
    	this.parentW=this.moveBox.parentNode.getBoundingClientRect().width+'px'||defaultW+'px';
    	this.wrapW=parseInt(this.parentW)*this.len+'px'||defaultW+'px';
    	console.log(this.parentW,'fffff')
    },
    computed: {
    	getParentW(){
    		console.log(this.parentW)
    		return this.parentW;
    	}
    },
    components: {
		MSwiperItem
    },
    props: {
      width:{
      	type:String,
        default:'100vw'
      },
      height:{
      	type:String,
        default:'auto'
      },
      showDisc: {
        type: Boolean,
        default: false
      },
      minMoveDis:{//滑动超过这个距离才跟随滑动
      	type:Number,
      	default:0
      },
      touchThreshold:{//滑动超过这个距离才切换
      	type:Number,
      	default:0
      },
      changeHandler:{
      	type:Function,
      	default:()=>{}
      }
    },
    methods: {
    	start(ev){
    		console.log('start');
    		this.moveBox.style.transition='none';
    		var target=ev.changedTouches[0];
    		this.startX=target.pageX;
    		this.endX=this.nowDis;
    		
    	},
    	move(ev){
    		var target=ev.changedTouches[0];
    		var disX=target.pageX-this.startX;
    		/*
    		 * 阻止浏览器的默认行文，否则在浏览器中滑动的时候整个屏幕会切换
    		 * 1.如果阻止了默认行为，在微信浏览器打开时页面出现滚动条的时候无法滚动
    		 * 1.如果不阻止默认行文，在手机浏览器打开就无法试下滑屏滚动
    		 */
//  		document.addEventListener('touchmove',this.preventDefault/*,{ passive: false}*/);
    		this.nowDis=disX+this.endX;
    		var currentDis=(target.pageX-this.startX);
    		var absDis=Math.abs(currentDis)>this.touchThreshold;
    		if(absDis){
    			this.moveBox.style.webkitTransform=this.moveBox.style.transform='translate3d('+this.nowDis+'px,0,0)';
    		}
    		
    	},
    	end(ev){
    		var target=ev.changedTouches[0];
    		var currentDis=(target.pageX-this.startX);
    		var absDis=Math.abs(currentDis)>this.minMoveDis;
    		if(absDis&&(currentDis<0)){
    			this.index+=1;
    		}else if(absDis&&(currentDis>0)){
    			this.index-=1;
    		}
    		this.index=this.index<0?0:this.index;
    		this.index=this.index>=this.len?this.len-1:this.index;
    		this.nowDis = -this.index*parseInt(this.parentW);
    		console.log(this.nowDis,'nowd')
			this.moveBox.style.transition='.5s';
			this.moveBox.style.webkitTransform =this.moveBox.style.transform ='translate3d('+this.nowDis+'px,0,0)';
			this.changeHandler(this.index);
    		
    	},
    	preventDefault (ev) {
			var ev = ev || event;
			if (ev.preventDefault) {
				ev.preventDefault()
			} else{
				ev.returnValue = false;
			}
		}
    	
    },
    watch: {
    	len:function(){
    		console.log('len change')
    	}
    },
  }

</script>
<style lang="less" scoped>
	.m-swiper{
		width: 100%;
		overflow: hidden;
	}
	.m-swiper-wrap{
		overflow: hidden;
		/*touch-action: none;*/
		.m-swiper-item{
			float: left;
			img{
				display: block;
			}
		}
	}
</style>