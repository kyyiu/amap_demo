<template>
    <div>
        <input type="text" placeholder="请输入关键词" :id="placeInput" v-model="place"/>
        <button @click="send">查找</button>
    </div>
</template>

<script>
import bus, {placeInput} from '@/eventBus/eventBus.js'
export default {
    data() {
        return {
            placeInput,
            place: ''
        }
    },
    mounted(){
        this.send()
    },
    methods: {
        send(){
            bus.$emit(placeInput, this.place)
        }
    }
}
</script>