<template>
    <div class="center">
        <div style="font-weight:bold; padding-left: 18px">visual code
            <!-- <input type="button" class="name" value="name" @click="showName"> -->
            <button class="name" @click="showName">
                name
                <svg fill="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg" aria-hidden="true" class="eye" style="color:black" v-show="show_name">
                    <path d="M12 15a3 3 0 100-6 3 3 0 000 6z"></path>
                    <path clip-rule="evenodd" fill-rule="evenodd" d="M1.323 11.447C2.811 6.976 7.028 3.75 12.001 3.75c4.97 0 9.185 3.223 10.675 7.69.12.362.12.752 0 1.113-1.487 4.471-5.705 7.697-10.677 7.697-4.97 0-9.186-3.223-10.675-7.69a1.762 1.762 0 010-1.113zM17.25 12a5.25 5.25 0 11-10.5 0 5.25 5.25 0 0110.5 0z"></path>
                </svg>
                <svg fill="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg" aria-hidden="true" class="eye" style="color:grey" v-show="!show_name">
                    <path d="M3.53 2.47a.75.75 0 00-1.06 1.06l18 18a.75.75 0 101.06-1.06l-18-18zM22.676 12.553a11.249 11.249 0 01-2.631 4.31l-3.099-3.099a5.25 5.25 0 00-6.71-6.71L7.759 4.577a11.217 11.217 0 014.242-.827c4.97 0 9.185 3.223 10.675 7.69.12.362.12.752 0 1.113z"></path>
                    <path d="M15.75 12c0 .18-.013.357-.037.53l-4.244-4.243A3.75 3.75 0 0115.75 12zM12.53 15.713l-4.243-4.244a3.75 3.75 0 004.243 4.243z"></path>
                    <path d="M6.75 12c0-.619.107-1.213.304-1.764l-3.1-3.1a11.25 11.25 0 00-2.63 4.31c-.12.362-.12.752 0 1.114 1.489 4.467 5.704 7.69 10.675 7.69 1.5 0 2.933-.294 4.242-.827l-2.477-2.477A5.25 5.25 0 016.75 12z"></path>
                </svg>
            </button>
            
        </div>
        <!-- <input type="button" class="clear" value="clear" @click="clearCanvas"> -->
        
        <!--input type="button" class="export" value="export" @click="exportToCsv"-->
        <!-- <input type="button" class="add" style="font-size:18px" value="+" @click="addVisualCodeList"> -->
        <svg fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg" aria-hidden="true" class="add" @click="addVisualCodeList">
            <path stroke-linecap="round" stroke-linejoin="round" d="M12 8v8m4-4h-8M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path>
        </svg>
    
        <div class="pers-bar">
            <div class="one-bar" v-for="item in visual_list" :key="item" :id="'bar_'+item">
                <MyCodesList  ref="code_window" @delete="deleteVisualCodeList" :index="item" :show_name="show_name"/>
            </div>
        </div>
    </div>
</template>

<style scoped>
.center{
    width: 100%;
    height: 100%;
    border: solid #a1caf1 2px; 
    border-radius: 5px; 
    background: #a1caf1; 
    position: relative;
}
.pers-bar{
    position: relative;
    display: flex;
    width: 100%;
    height: 100%;
}

.one-bar{
    position: relative;
    width: 100%;
    height: 100%;
    margin-left: 2px;
    margin-right: 2px;
    transform: all 0.3s
}

 .name, .export{
    border: 1px black solid;
    border-radius: 3px;
    margin-left: 12px;
    margin-bottom: 3px;
    padding-right: 25px;
    position: relative;
}

.add{
    position: absolute;
    right: 5px;
    top: 0px;
    width: 25px;
    color: rgb(24, 151, 168);
}

.eye{
    padding-left: 5px;
    width: 15px;
    position:absolute;
    top: 50%;
    transform:translate(0,-50%);
}
</style>

<script>
import MyCodesList from './MyCodesList.vue';
import bus from '../util/eventBus';
import { interactive_list } from '../util/codeList';
import { color_for_highlight } from '../util/colorMapping';

export default{
    emits:['change'],
    components:{ MyCodesList },
    data(){
        return{
            show_name: false,
            visual_list: [0],
            latest_idx: 0
        }
    },

    methods:{
        showName(){
            this.show_name = !this.show_name;
        },

        exportToCsv(){

        },

        addVisualCodeList(){
            if(this.visual_list.length >= 3){
                alert('The visual codes list exceed its upper limit')
                return
            }
            this.visual_list.push(++this.latest_idx)
            this.$emit('change', this.visual_list.length)
        },

        deleteVisualCodeList(index){
            if(this.visual_list.length<=1){
                alert('This is the last visual code')
                return
            }
      
            for(let i=0; i<this.visual_list.length; i++){
                if(Number(index) === this.visual_list[i]){
                    this.visual_list.splice(i, 1)
                    bus.all.get("updateVisualCode").splice(i, 1)
                    bus.all.get("updateDataOrder").splice(i, 1)
                    bus.all.get("changeRectOpacity").splice(i, 1)
                    bus.all.get("changeLineOpacity").splice(i, 1)
                    bus.all.get("clickForLine").splice(i+1, 1)
                    // bus.all.get("showSimilarityCode").splice(i, 1)
                    // bus.all.get("calculateDistance").splice(i, 1)
                    bus.all.get("alignOrder").splice(i, 1)
                    bus.all.get("synchronizeScroll").splice(i, 1)
                    bus.all.get("updatePersistence").splice(i, 1)
                    break
                }
            }
            this.$emit('change', this.visual_list.length)
        },
 
    },
    created(){
        bus.on("clearCanvas", msg=>{
            for(let i=0; i<this.visual_list.length; i++)
                this.$refs.code_window[i].clearHighlight()
            for(let key in color_for_highlight)
                delete color_for_highlight[key]
            interactive_list.splice(0, interactive_list.length)
            bus.emit("clickForLine", -1)
        })
    },

}
</script>