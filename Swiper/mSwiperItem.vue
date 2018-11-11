<template>
	<div class="m-swiper-item" :style="{'width':width}" @click="handler">
		<slot></slot>
	</div>
</template>

<script>
  export default {
  	name:'mSwiper',
    data() {
      return {
      	height:'',
      	width:''
      }
    },
    created(){
    },
    mounted() {
    	this.height=this.$parent.$props.height||'100%';
    	this.width=this.$parent.$refs.tab.parentNode.getBoundingClientRect().width+'px'||'100vw';
    },
    computed: {
    	
    },
    components: {

    },
    props: {
      clickHandler:{
      	type:Function,
      	default:()=>{}
      }
    },
    methods: {
    	handler(){
    		this.clickHandler();
    	}
    },
    watch: {
    },
  }

</script>
<style lang="less" scoped>
	.m-swiper-item{
		/*width: 100vw;*/
	}
</style>