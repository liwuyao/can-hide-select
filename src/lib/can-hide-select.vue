<style scoped>
	*{
		text-decoration: none;
		list-style-type: none;
		margin: 0;
		padding: 0;
	}
	.lwy-select{
		background: yellow;
		border-radius: 3px;
	}
	.lwy-option{
		position: absolute;
		left: 0;
		top: 0;
		width: 100%;
	}
	.lwy-option li{
		padding: 0 10px 0 10px;
		overflow: hidden;
    	white-space: nowrap;
    	text-overflow:ellipsis;
    	cursor: pointer;
	}
	.lwy-option li:hover{
		background-color: #f5f7fa;
		color: #409eff;
	}
	.optionOutBox{
		position: relative;
		overflow: hidden;
	}
	.select-value{
		padding: 10px 0;
		padding-left: 3px;	
		padding-right: 20px;	
		position: relative;
		width: 120px;
		cursor: pointer;
	}
	.xialaicon{
		position: absolute;
		right: 0;
		top: 12px;
		cursor: pointer;
		transform-style:preserve-3d;
	}
/*	省略class*/
    .hide{
    	overflow: hidden;
    	white-space: nowrap;
    	text-overflow:ellipsis;
    }
</style>
<template>
  <div class="lwy-select" :style="valueStyle">
  	<div class="select-value" ref="selcetVal" :style="selectStyle" :class="{hide:hideSate}">
  		{{selectVal}}
  		<i class="iconfont icon-vue-select-xiala xialaicon" v-on:click="openOption()" ref=xiala></i>
  	</div>
  	<div ref="optionOutBox" class="optionOutBox">
  		<ul class="lwy-option" ref="optionBox">
	  		<li :style="optionStyle" ref="option" v-for="item in optionVals" v-on:click="chooseOption(item)" class='lwy-option'>{{item}}</li>
	  	</ul>
  	</div>
  </div>
</template>

<script>
export default {
  props: ['data','config'],
  name: 'canHideSelect',
  data () {
    return {
      openState:false,
      hideSate:false,
      msg: '',
      optionCalssName:'',
      selectVal:'请输入',
      selectStyle:'',
      valueStyle:'',
      optionStyle:'',
      optionVals:[]
    }
  },
  created(){
  	this.optionVals = this.data;
  	for(let i in this.config){
  		this[i] = this.config[i]
  	}
  },
  mounted: function () {
  	if(this.optionVals[0]){
  		this.selectVal = this.optionVals[0]
  	}
	this.$nextTick(function () {
	    let Elm = this.$refs.selcetVal;
	   	this.fontHide(Elm);
	   	let options = this.$refs.option;
	   		for(let i = 0;i<options.length;i++){
	   			if(this.optionCalssName){
	   				options[i].className = this.optionCalssName;
	   			}
	   			this.fontHide(options[i]);
	   		}
	  })
	document.body.onclick=(event)=>{
		let name = event.target.className;
		if(name !== 'lwy-select' && name !== 'lwy-select hide' && name !== 'iconfont icon-vue-select-xiala xialaicon' && name !== 'optionOutBox' && name !== 'optionBox' && name !== 'lwy-option'){
			if(this.openState){
				this.openOption();
			}
		}
	}
  },
  watch:{
  	selectVal:function(){
  		this.hideSate = false;
  		let Elm = this.$refs.selcetVal;
	   	let font = Elm.style.fontSize;
	    let val = this.selectVal;
	    let pxIndex = font.indexOf('px');
	    let fontHeight = Number(font.slice(0,pxIndex));
	    setTimeout(()=>{
	    	let Elmheight = Elm.clientHeight - 20;
	    	if(Math.floor(Elmheight/fontHeight) >= 2){
	    		this.hideSate = true;
	    		Elm.title = val;
	    	}else{
	    		Elm.title = '';
	    	}
	    },0)
  	}
  },
  methods:{
  	openOption(){
  		if(this.optionVals.length === 0) return;
  		let optionOutBox = this.$refs.optionOutBox;
  		let Elmxiala = this.$refs.xiala;
  		let options = this.$refs.option;
  		let len = options.length;
  		let elm = options[0];
		let limitHeight = elm.offsetHeight * len;
		let outBoxheight;
		let deg;
		if(!this.openState){
			outBoxheight = 0;
			deg = 0;
			this.openState= true;
			let openxialaTimer=setInterval(()=>{
				deg+=5;
				Elmxiala.style.transform = 'rotateZ('+ deg + 'deg)'
				if(deg>=180){
					Elmxiala.style.transform = 'rotateZ('+ 180 + 'deg)'
					clearInterval(openxialaTimer);
				}
			},5)
			let openTimer=setInterval(()=>{
					outBoxheight += 5;
					optionOutBox.style.height = outBoxheight + 'px';
					if(outBoxheight>=limitHeight){
						optionOutBox.style.height = limitHeight + 'px';
						clearInterval(openTimer);
					}
				},5);
		}else{
			outBoxheight = limitHeight;
			this.openState = false;
			deg = 180;
			let closexialaTimer=setInterval(()=>{
				deg-=5;
				Elmxiala.style.transform = 'rotateZ('+ deg + 'deg)'
				if(deg<=0){
					Elmxiala.style.transform = 'rotateZ('+ 0 + 'deg)'
					clearInterval(closexialaTimer);
				}
			},5)
			let closeTimer=setInterval(()=>{
					outBoxheight -= 5;
					optionOutBox.style.height = outBoxheight + 'px';
					if(outBoxheight <= 0){
						optionOutBox.style.height = 0;
						clearInterval(closeTimer);
					}
				},5);
		}
  	},
//	省略函数
	fontHide(Elm){
		this.hideSate = true;
		let font = Elm.style.fontSize;
	    let val = Elm.innerText;
	    let pxIndex = font.indexOf('px');
	    let fontHeight = Number(font.slice(0,pxIndex));
	    let Elmheight = Elm.clientHeight - 20;
	    	if(Math.floor(Elmheight/fontHeight) >= 2){
	    		this.hideSate = true;
	    		Elm.title = val;
	    	}
	},
	chooseOption(val){
		this.selectVal = val;
		this.openOption();
		this.$emit('selectVal',this.selectVal)
	},
//	返回函数
	selectRes(){
		this.$emit('selectVal',this.selectVal)
	}
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->

