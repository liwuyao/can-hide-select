<style scoped>
	@import url("./iconfont.css");
	*{
		text-decoration: none;
		list-style-type: none;
		margin: 0;
		padding: 0;
	}
	.lwy-select{
		border-radius: 3px;
		width: 140px;
		position: relative;
	}
	.lwy-option{
		position: absolute;
		left: 0;
		top: 0;
		width: 100%;
	}
	.lwy-option li{
	/*	padding: 0 10px 0 10px;*/
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
		width: 100%;
		position: absolute;
		top:41px;
		z-index: 2;
		overflow: hidden;
	}
	.select-value{
		padding: 10px 0;
/*		padding-left: 3px;	*/
		padding-right: 20px;	
		position: relative;
		width: 120px;
		cursor: pointer;
		overflow: hidden;
    	white-space: nowrap;
    	text-overflow:ellipsis;
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
    .icon {
		width: 1em; height: 1em;
		vertical-align: -0.15em;
		fill: currentColor;
		overflow: hidden;
	}
</style>
<template>
  <div class="lwy-select" :style="selectStyle">
  	<div class="select-value" ref="selcetVal" :style="valueStyle">
  		{{selectVal}}
  		<i class="iconfont icon-vue-select-xiala xialaicon" v-on:click="openOption()" ref=xiala :style="iconStyle"></i>
  	</div>
  	<div ref="optionOutBox" class="optionOutBox" :style="optionOutBoxStyle">
  		<ul class="lwy-option" ref="optionBox" :style="optionBoxStyle">
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
      isLable:false,
      msg: '',
      optionCalssName:'',
      selectVal:'请输入',
      selectStyle:'',
      valueStyle:'',
      optionStyle:'',
      iconStyle:'',
      optionBoxStyle:'',
      optionOutBoxStyle:'',
      valueAddPadding:0,
      optionAddPadding: 0,
      optionVals:[],
    }
  },
  created(){
  	this.optionVals = this.data;
  	for(let i in this.config){
  		this[i] = this.config[i]
  	}
  	if(this.isLable){
  		let ary = [];
  		for(let data of this.optionVals){
  			ary.push(data.lable);
  		}
  		this.optionVals = ary;
  	}
  },
  mounted: function () {
  	if(this.optionVals[0]){
  		if(this.isLable){
  			this.selectVal = this.optionVals[0];
  		    this.$emit('selectVal',this.selectVal)
  		}
  	}
	this.$nextTick(function () {
	    	this.selectValFontHide();
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
  		this.selectValFontHide();
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
		let font = Elm.style.fontSize;
	    let val = Elm.innerText;
	    let width = Elm.clientWidth - this.optionAddPadding;
	    let pxIndex = font.indexOf('px');
	    let fontHeight = Number(font.slice(0,pxIndex));
	    let egWidth = fontHeight*0.55;
  		let vals = Elm.innerText;
  		let computWidth = 0;	
  		for(let i = 0; i < val.length; i++){
           if(val.charCodeAt(i) > 255){
             computWidth += fontHeight;
           }else {
             computWidth += egWidth;
           }
         }
  		if(computWidth>=width){
  			Elm.title = val;
  		}else{
  			Elm.title = ''
  		}
	},
//	selectval隐藏函数
	selectValFontHide(){
		this.hideSate = false;
  		let Elm = this.$refs.selcetVal;
  		let width = Elm.clientWidth - 20 - this.valueAddPadding;
  		let font = Elm.style.fontSize;
	    let val = this.selectVal;
	    let pxIndex = font.indexOf('px');
	    let fontHeight = Number(font.slice(0,pxIndex));
	    let egWidth = fontHeight*0.55;
  		let vals = Elm.innerText;
  		let computWidth = 0;	
  		for(let i = 0; i < val.length; i++){
           if(val.charCodeAt(i) > 255){
             computWidth += fontHeight;
           }else {
             computWidth += egWidth;
           }
         }
  		if(computWidth>=width){
  			Elm.title = val;
  		}else{
  			Elm.title = ''
  		}
	},
	chooseOption(val){
		this.selectVal = val;
		this.openOption();
		if(this.isLable){
			for(let data of this.data){
	  			if(data.lable === this.selectVal){
	  				this.$emit('selectVal',data.value)
	  			}
	  		}
		}else{
			this.$emit('selectVal',this.selectVal)
		}
	},
//	返回函数
	selectRes(){
		if(this.isLable){
			for(let data of this.data){
	  			if(data.lable === this.selectVal){
	  				this.$emit('selectVal',data.value)
	  			}
	  		}
		}else{
			this.$emit('selectVal',this.selectVal)
		}
	}
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->

