<template>
<div id="app" v-if='this.dateShow'>
    <div class='selectDate'>
        选择日期
        <span class='dateClose iconfont' v-on:click='handleDateClose'>&#xe63f;</span>
    </div>
    <div class="flex">
        
        <div class='time'>
            <calendar :lunar="calendar1.lunar" :value="calendar1.value" :begin="calendar1.begin" :end="calendar1.end" :weeks="calendar1.weeks" :months="calendar1.months" @select="calendar1.select"></calendar>
        </div>

    </div>

    <transition name="fade">
    <div class="calendar-dropdown" :style="{'left':calendar3.left+'px','top':calendar3.top+'px'}" v-if="calendar3.show">
        <calendar :zero="calendar3.zero" :lunar="calendar3.lunar" :value="calendar3.value" :begin="calendar3.begin" :end="calendar3.end" @select="calendar3.select"></calendar>
    </div>
    </transition>

    <transition name="fade">
    <div class="calendar-dialog" v-if="calendar4.show">
        <div class="calendar-dialog-mask" @click="closeByDialog"></div>
        
        <div class="calendar-dialog-body">
            <calendar :range="calendar4.range" :zero="calendar4.zero" :lunar="calendar4.lunar" :value="calendar4.value"  @select="calendar4.select"></calendar>
        </div>
        
    </div>
    </transition>
</div>
</template>

<script>
import calendar from './calendar.vue'
let recieveDate='';
export default {
    name: 'app',
    components: {
        calendar
    },
    beforeCreate(){
		this.year=new Date().getFullYear();
        this.month=new Date().getMonth()+1;
        this.day=new Date().getDate();
    },
    props:['dateShow'],
    data(){
        return {
            calendar1:{
                value:[this.year,this.month,this.day],
                weeks:['日', '一', '二', '三', '四', '五', '六'],
                months:['一月', '二月', '三月', '四月', '五月', '六月', '七月', '八月', '九月', '十月', '十一月', '十二月'],
                events:{
                    '2017-8-30':'$408','2017-8-31':'$460'
                },
                select(value){
                    if(value){
                        recieveDate=value.toString();
                    }
                }
            },
            calendar3:{
                display:"2018/02/16",
                show:false,
                zero:true,
                value:[2018,2,16],
                lunar:true,
                select:(value)=>{
                    this.calendar3.show=false;
                    this.calendar3.value=value;
                    this.calendar3.display=value.join("/");
                }
            },
            calendar4:{
                display:"2018/02/16 ~ 2019/02/16",
                show:false,
                range:true,
                zero:true,
                value:[[2018,2,16],[2019,2,16]],
                lunar:true,
                select:(begin,end)=>{
                    console.log(begin,end)
                    this.calendar4.show=false;
                    this.calendar4.value=[begin,end];
                    this.calendar4.display=begin.join("/")+" ~ "+end.join("/");
                }
            },
        }
    },
    methods:{
        openByDrop(e){
            this.calendar3.show=true;
            this.calendar3.left=e.target.offsetLeft+19;
            this.calendar3.top=e.target.offsetTop+70;
           
            e.stopPropagation();
            window.setTimeout(()=>{
                document.addEventListener("click",(e)=>{
                    this.calendar3.show=false;
                    document.removeEventListener("click",()=>{},false);
                },false);
            },1000)
        },
        openByDialog(){
            this.calendar4.show=true;
        },
        closeByDialog(){
            this.calendar4.show=false;
        },
        handleDateClose(){
            this.$emit('date',{
                'show':false,
                'date':recieveDate
            });
        }
    }
}
</script>

<style scoped>
@import "../../../assets/font/iconfont.css";
#app{
	width:100%;
	height:100%;
	font-size:.28rem;
	position:fixed;
	bottom:0;
	left:0;
	background:rgba(0,0,0,.4);
	z-index:100;
	overflow:hidden;
}
.flex{
	width:100%;
    height:6.6rem;
    position:absolute;
    padding:0;
    bottom:0;
    left:0;
    background:#fff;
    box-sizing: border-box;

    display: -webkit-box;
    -webkit-box-pack: start;
    -webkit-box-align: start;

    display: -webkit-flex;
    -webkit-justify-content: space-between;
    -webkit-align-items: top;

    display: flex;
    justify-content: space-between;
    align-items: top;
    flex-flow:row wrap
}
.selectDate{
    width:100%;
    line-height:1rem;
    font-size:.34rem;
    position:absolute;
    left:0;
    bottom:6.5rem;
    background:#fff;
    text-align:center;
}
.dateClose{
    color:#9e9e9e;
    font-size:.36rem;
    position:absolute;
    right:.3rem;
    background:#fff;
}
.flex>div{
    width:100%;
    position: relative;
}
.flex>div>span{
    position: absolute;
    left:0px;
    top:0px;
    padding:2px 10px;
    font-size:10px;
    border-radius:0 0 2px 0;
    background:#ea6151;
    color:#fff;
}
.flex>div>input{
    box-sizing: border-box;
    width:100%;
    margin-top:20px;
    border-radius: 2px;
    border:1px solid #dedede;
    padding:10px;
    font-size: 16px;
    padding-left: 36px;
    color:#666;
}

.fade-enter-active,
.fade-leave-active {
    transition: all .5s ease-in-out;
}
.fade-enter,.fade-leave-active{
    opacity: 0;
    transform: translateY(-10px);

}

/*下拉框*/
.calendar-dropdown{
    background: #fff;
    position: absolute;
    left:0;
    top:0;
    padding:20px;
    border: 1px solid #eee;
    border-radius: 2px;
}
.calendar-dropdown:before {
    position: absolute;
    left:30px;
    top: -10px;
    content: "";
    border:5px solid rgba(0, 0, 0, 0);
    border-bottom-color: #DEDEDE;
}
.calendar-dropdown:after {
    position: absolute;
    left:30px;
    top: -9px;
    content: "";
    border:5px solid rgba(0, 0, 0, 0);
    border-bottom-color: #fff;
}

/*弹出框*/
.calendar-dialog{
    position: absolute;
    left:0;
    top:0;
    right:0;
    bottom:0;
}

.calendar-dialog-mask{
    background:rgba(255,255,255,.5);
    width:100%;
    height:100%;
}

.calendar-dialog-body{
    background: #fff;
    position: absolute;
    left:50%;
    top:50%;
    transform: translate(-50%,-50%);
    padding:20px;
    border: 1px solid #eee;
    border-radius: 2px;
}
</style>