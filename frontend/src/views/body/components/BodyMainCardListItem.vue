<template>
   <div class="bg-white p-8 rounded-lg shadow-lg relative hover:shadow-2xl transition duration-500 word-card">
      <h1 class="text-2xl text-gray-800 font-semibold mb-3">{{ room.title }}</h1>
      <p class="text-gray-600 leading-6 tracking-normal">방장 : {{ room.member.nickname }}</p>
      <p class="text-gray-600 leading-6 tracking-normal">참여인원 : {{ room.currentNum }}/{{room.restrictNum}}</p>
      <div></div>
      <button class="py-2 px-4 mt-8 text-white rounded-md shadow-xl join-button" @click="joinBodyRoom" v-if="!room.roomStatus && !(room.currentNum === room.restrictNum)">입장하기</button>
      <button class="py-2 px-4 mt-8 text-white rounded-md shadow-xl nojoin-button" @click="cantJoin" v-if="room.roomStatus || room.currentNum == room.restrictNum">입장불가</button>
      <div>
        <span class="absolute py-2 px-8 text-sm text-white top-0 right-0 rounded-md transform translate-x-2 -translate-y-3 shadow-xl mode-name">{{ mode }}</span>
      </div>
    </div>
</template>

<script>
import axios from 'axios';
import { mapGetters } from 'vuex';
// <router-link :to="{ name: 'ChatDetailView', params: { mode : mode, sessionName : room.title, isChatRoomCreate : 'false'}}" :sessionName="room.sessionId" :isChatRoomCreate="false">입장하기</router-link>

axios.defaults.headers.post['Content-Type'] = 'application/json';


export default {
    props : {
        room : Object,
        mode : String
    },
    created(){
        
    },
  data () {
        return {
            token : ''
        }
    },
  methods: {
    
    joinBodyRoom(){
      if(this.isLoggedIn){
        axios({
            url : `/api/body-game/rooms/`+this.room.roomSequence,
            method : 'post',
            headers : this.authHeader})
        .then(res => {console.log(res),
        this.$router.push({ name: 'BodyDetailView', params: { mode : this.mode, sessionName : this.room.title, token : res.data.data.token,roomSequence : this.room.roomSequence,
        host : this.room.member}})})
        .catch(err => console.log(err,123 ))
        }
      else{
        alert('로그인이 필요합니다!')
        this.$router.push({ name: 'LoginView'})
      }
      },
      cantJoin(){
        if (this.room.currentNum === this.room.restrictNum){
          alert('정원이 가득 찼습니다.')
        } else {
          alert('게임이 시작되었습니다.')
        }
      }
    },
    computed:{
        ...mapGetters(['authHeader','isLoggedIn'])
    }
    }
    
       
    


</script>

<style scoped>
.word-card {
    width : 275px;
}

.join-button {
  background-color: #f7bfb4;
  justify-content: center;
}

.nojoin-button {
  background-color: gray;
  justify-content: center;
}

.mode-name {
  background-color: #f7bfb4;
}
</style>