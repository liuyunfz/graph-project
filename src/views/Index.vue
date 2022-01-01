<template>
    <div>
        <div id='canvas-div'> 
            <canvas id="canvas" @click="drawPoint($event)" width="800" height="640"/>
        </div>
        <div style="float:left; width:320px; padding: 18px 0px;">
            <el-card class="box-card" style="text-align: center;padding: 18px 0px;">
            <div class="text item">
                <el-button type="primary" round @click="DFS">DFS</el-button>
                <el-button type="primary" round @click="BFS">BFS</el-button>              
            </div>
            </el-card>
            <el-card class="box-card" style="padding: 6px 0px; margin-top: 30px;">
            <div slot="header" class="clearfix">
                <span>Log</span>
                <el-button style="float: right; padding: 3px 0" type="text" @click="clearLog">清空日志</el-button>
            </div>
            <p v-for="o in logDict" :key="o" class="text item">
                {{'Info: ' + o }}
            </p>
            </el-card>
            
        </div>
    </div>
</template>

<script>
import { Message } from 'element-ui';
export default {
    data() {
    return {
        context: {},
        point_id:0,
        v_map:{

        },
        tmpPoint:[],
        isChoice:false,
        lineTable:{},
        visitedPoint:{},
        logDict:[],
        tmpStr:'',
        pointQueue:[]
    }},
    mounted() {
        const canvas = document.querySelector('#canvas')
        this.context = canvas.getContext('2d')
    },
    methods:{
        resetLineColor(){
            console.log(this.lineTable);
            for(let p in this.lineTable){
                let len = this.lineTable[p].length;
                for(let q=0;q<len;q++){                                    
                    this.drawLine(p.split(','),this.lineTable[p][q],'#0000FF')
                }
            }
        },
        clearLog(){
            this.logDict=[]
        },
        sleep(d){
        for(var t = Date.now();Date.now() - t <= d;);
        },
        DFS_main(lastpoint,point){
            if(point in this.visitedPoint){              
                return;
            }else{
                if(lastpoint!=point){     
                    setTimeout(this.drawLine,200,lastpoint,point,'#00FF00')
                }
                // canvas是向图象缓冲区里绘制的，浏览器以一定的刷新频率从缓冲区里取出图象数据显示。如果你在浏览器的2次刷新间隔之间先画一个红色矩形，然后紧接着再在相同的位置画一个大小相同的蓝色矩形，那么我们只能看到蓝色矩形（不是刷新频率快我们的眼睛跟不上，而是红色矩形压根不会被浏览器渲染到屏幕上）
                this.tmpStr +=("->"+this.v_map[point])
                this.$set(this.visitedPoint,point,true)
                let unionPoint = this.lineTable[point]             
                for(let item in unionPoint){
                    if(unionPoint[item] in this.visitedPoint){
                        continue;
                    } else{
                        this.DFS_main(point,unionPoint[item])
                    }            
                    
                }
            }
                    
        },
        DFS(){
            if(this.tmpPoint.length==0){
                Message.warning('请先选择一个DFS的起始点');
            }else{
                this.resetLineColor();
                this.visitedPoint={}
                let p_begin = this.tmpPoint.shift(); 
                this.logDict.push('从点'+this.v_map[p_begin]+'开始DFS遍历')  
                this.tmpStr=''            
                this.DFS_main(p_begin,p_begin);               
                // this.context.globalCompositeOperation="destination-over";
                this.setPointColor(p_begin,'#FF0000')
                this.logDict.push(this.tmpStr)
                this.isChoice=false
            }
        },
        BFS(){
            if(this.tmpPoint.length==0){
                Message.warning('请先选择一个BFS的起始点');
            }else{
                this.resetLineColor();
                this.visitedPoint={}
                let p_begin = this.tmpPoint.shift(); 
                this.logDict.push('从点'+this.v_map[p_begin]+'开始BFS遍历')  
                this.tmpStr=''            
                this.BFS_main([p_begin,p_begin]);               
                // this.context.globalCompositeOperation="destination-over";
                this.setPointColor(p_begin,'#FF0000')
                this.logDict.push(this.tmpStr)
                this.isChoice=false
            }
        },
        BFS_main(point){
            if(point[0] in this.visitedPoint){              
                return;
            }else{
                if(point[0]!=point[1]){     
                    setTimeout(this.drawLine,200,point[0],point[1],'#00FF00')
                }
                this.tmpStr +=("->"+this.v_map[point[0]])
                this.$set(this.visitedPoint,point[0],true)
                let unionPoint = this.lineTable[point[0]]             
                for(let item in unionPoint){
                    if(unionPoint[item] in this.visitedPoint){
                        continue;
                    } else{
                        this.pointQueue.push([unionPoint[item],point[0]])
                    }            
                    
                }               
                while(this.pointQueue.length!=0){
                    let tmp_point = this.pointQueue.shift();
                    if(!(tmp_point[0] in this.visitedPoint)){
                        
                        this.BFS_main(tmp_point);
                    }
                    
                }
            }
        },
        makeUnion(p1,p2){
            
            if(p1 in this.lineTable){
                this.lineTable[p1].push(p2)
            }else{
                this.$set(this.lineTable,p1,[p2])
            }
        },
        drawLine(p1,p2,color){
            this.context.lineWidth = 3;
            this.context.beginPath();
            this.context.moveTo(p1[0],p1[1]);
            this.context.lineTo(p2[0],p2[1]);
            this.context.strokeStyle = color;    
            this.context.fill();
            this.context.stroke();
        },
        setPointColor(p,color){
            this.context.clearRect(p[0],p[1],13,13)
            this.context.fillStyle = color;
            this.context.fillRect(p[0], p[1], 8, 8); 
        },
        drawPoint(e) { 
            let p = this.isAlreadExist(e.clientX,e.clientY);
            if(p){
                this.setPointColor(p,'#0000FF')
                this.tmpPoint.push(p);
                if(this.isChoice){
                    let  p1=this.tmpPoint.shift();
                    let  p2=this.tmpPoint.shift();
                    this.drawLine(p1,p2,'#0000FF');
                    this.setPointColor(p1,'#FF0000')
                    this.setPointColor(p2,'#FF0000')
                    this.logDict.push('连通点'+this.v_map[p1] +'与点'+this.v_map[p2])
                    this.makeUnion(p1,p2)
                    this.makeUnion(p2,p1)
                    this.isChoice=false;
                }else{
                    this.isChoice=true;
                }
            }else{
                if(this.isChoice){
                    let tmp_p = this.tmpPoint.shift();
                    if(tmp_p){
                        this.setPointColor(tmp_p,'#FF0000')
                        this.tmpPoint=[];
                    }
                    
                    this.isChoice=false;
                }
                // 设置绘制颜色
                this.context.fillStyle = "#FF0000";
                // 绘制成矩形
                this.context.fillRect(e.clientX, e.clientY, 8, 8);   
                // 设置字体样式
                this.context.font = "12px 宋体";
                // 绘制文字
                this.context.fillText("V"+this.point_id, e.clientX+2, e.clientY-2);
                this.$set(this.v_map,[e.clientX,e.clientY],"V"+this.point_id);
                this.point_id++;
                this.logDict.push('创建点 V'+(this.point_id-1))
            }
        },
        deleteAllPrint(){
            this.context.clearRect(0,0,canvas.width,canvas.height);  
        },
        isAlreadExist(pointX,pointY){
            for (let point in this.v_map){
                let p = point.split(',');
                if(Math.abs(pointX-p[0])<=8&&Math.abs(pointY-p[1])<=8){
                    return p;
                }
            }
            return false;
        }

    }

}
</script>

<style>
#canvas{
    border: 1px solid blue ;
    display: block;
}
* {
    margin: 0;
    padding: 0;
}
#canvas-div{
    float: left;
    height: 648px;
    width: 900px;
}
</style>