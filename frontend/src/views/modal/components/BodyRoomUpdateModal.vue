<template>
<div v-if="open" class="bodymodal bg-white" tabindex="-1" >
  <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="staticBackdropLabel">몸으로 말해요 방 설정</h5>
        <button @click="$emit('update:open', !open)" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
      </div>
      <br>
      <div class="modal-body">
        <div class="row_box">
          <h6>제목 : </h6>
          <input type="text" v-model="sessionName" placeholder="방 이름을 입력해주세요" class="title-input" />
          <!-- <p v-if="!sessionName" style="color:red; font-size:13px; font-style:italic; margin-top:10px;">방 이름을 입력해주세요.</p> -->
        </div>
        <br>
        <div class="row_box">
          최대 정원 :  
          <select v-model="maxUsercnt" name="" id="">
            <option v-for="m in contents.maxUser" :value="m.value" :key="m.value">
              {{ m.text }}
            </option>
          </select>명
        </div>
        <br>
        <div class="row_box">
        문제수 : <select v-model="problemcnt" name="" id="">
          <option v-for="problem in contents.problems" :value="problem.value" :key="problem.value">
            {{ problem.text }}
          </option>
        </select>
      </div>
      <br>
<div class="row_box">
        난이도 : <select v-model="levelcnt" name="" id="">
          <option v-for="lev in contents.level" :value="lev.value" :key="lev.value">
            {{ lev.text }}
          </option>
        </select>
      </div>
      <br>
<div class="row_box">
        제한시간 : <select v-model="timecnt" name="" id="">
          <option v-for="time in contents.timeLimit" :value="time.value" :key="time.value">
            {{ time.text }}
          </option>
        </select>
      </div>
      <br>
      </div>
      <div class="modal-footer" >
        <span  class="mt-3 btn-animate" data-bs-dismiss="modal" type="button" cursor="pointer" v-if="sessionName" @click="updateRoom" >변경</span>
        <!-- <span @click="$emit('update:bodycreateopen', !bodycreateopen)" type="button" class="btn-animate" data-bs-dismiss="modal">Close</span>         -->
      </div>


      <!-- <div class="modal-footer">
      <button class="mt-3" v-if="sessionName" @click="updateRoom">
      <span class='btn-animate'>변경</span>
      </button> -->
      <!-- <button @click="$emit('update:open', !open)" type="button" class="bg-gray-500 hover:bg-gray-700 text-white font-bold py-2 px-3 rounded" data-bs-dismiss="modal">Close</button> -->
      <!-- </div> -->
  </div>
</div>
</template>

<script>
import axios from "axios";
import { mapGetters } from 'vuex';

const SERVER_URL = process.env.VUE_APP_SERVER_URL


export default {
  name: 'BodyRoomUpdateModal',
  
  data () {
    return {
    maxUsercnt : 0,
    problemcnt : 0,
    levelcnt : 0,
    timecnt : 0,
    contents: {
        maxUser: [
          { text: '2', value: 2 },
          { text: '3', value: 3 },
          { text: '4', value: 4 },
          { text: '5', value: 5 },
          { text: '6', value: 6 },
        ],
        level: [
          { text: '1', value: 1 },
          { text: '2', value: 2 },
          { text: '3', value: 3 },
          { text: '4', value: 4 },
          { text: '5', value: 5 },        
        ],
        problems: [
          { text: '3', value: 3 },
          { text: '4', value: 4 },
          { text: '5', value: 5 },
          { text: '6', value: 6 },
          { text: '7', value: 7 },
          { text: '8', value: 8 },
          { text: '9', value: 9 },
          { text: '10', value: 10 },
        ],
        timeLimit: [
          { text: '15', value: 15 },
          { text: '30', value: 30 },
          { text: '45', value: 45 },
          { text: '60', value: 60 },
        ],

      }
    }
  },
  props :{
    open : Boolean,
  },
computed : {
    ...mapGetters(['authHeader'])},
  methods : {
    isClose() {
      console.log(this.open)
      this.$emit('update:open', false)
    },
    updateRoom(){
    axios(
      { url : `/api/word-game/rooms/${this.$route.params.roomSequence}`,
        method : 'put',
        data : {
        title : this.sessionName,
        restrict_num : this.maxUsercnt,
        problem_num : this.problemcnt
         },
       headers: {Authorization : this.$store.getters.authHeader.Authorization},
       })
    .then(res => {
      alert('방설정 변경완료!')
      this.isClose()

    })
     .catch(err => {
          console.log('에러발생!')
          if (err.response.data.status === 'sucess'){
            console.log('성공!!!!!!')
          }
          else{
            console.log('실패!!!!!!!')
            axios({
            url : `/api/word-game/rooms/${this.$route.params.roomSequence}`,
            method : 'put',
            data : {
            title : this.sessionName,
            restrict_num : this.maxUsercnt,
            problem_num : this.problemcnt
          },
              headers: this.$store.getters.authHeader,
              
            }).then(res =>{
              alert('방설정 변경완료!')
              this.isClose()
              const accessToken = res.headers.authorization
              this.$store.actions.member.reissuanceToken(accessToken)
            }).catch(err => {
              console.log(err.response.data)
            })
          }

        })
        
    
  },

    // room_info() {
    //     this.$store.dispatch('roomInfo',this.contents)
    //   },
    //   joinSession() {
    //     // event?
    //     event.preventDefault();
    //     return new Promise((resolve, reject) => {
    //             axios.defaults.headers.common[
    //                 "Authorization"
    //             ] = `Bearer ${this.$store.state.accessToken}`;
    //             axios.post(`${SERVER_URL}/conferences`, this.contents)
    //                 .then((res) => {
    //                     console.log('sdsdsdsd')
    //                     // console.log(commit);
    //                     console.log(res.data.roomId)
    //                     // this.$store.dispatch('roomInfo',this.contents)
    //                     this.$store.dispatch('joinSession',res.data.roomId)
    //                     this.$router.push({ name: "Room" , params: {roomid: res.data.roomId }});
                        
    //                     resolve();
    //                 })
    //                 .catch((error) => {
    //                     reject(error);
    //                 })
    //         })
    //   },
  }
}
</script>


<style scoped>
.bodymodal { 
  padding: 2%;
  position: absolute;
  display : flex; 
  /* top: 16%; */
  bottom: 6%;
  left: 50%;
  width: 300px;
  margin-left: -150px; 
  height : auto;
  z-index: 90;
  visibility: visible;
  opacity: 100;
  border: 0.05rem solid rgb(76,76,76,0.4);
  border-radius: 6% 6% 6% 6%;
  /* border: 0.1rem solid rgb(140,140,140,0.4);
  border-radius: 6% 6% 6% 6%; */
  /* background-color: transparent; */
}


.modal-title {
  font-size: 1.2rem;
  
}

.modal-content {
  /* padding: 8%; */
}

.title-input {
  font-size: 1.3rem;
  border: 0.1rem solid grey;
}

.roomTitle{
  text-shadow: 5px 5px 70px rgba(190, 209, 212, 0.582);
  font-size: 70px;
  background: linear-gradient(to bottom,#a769d6 ,#6f92d8);
   -webkit-background-clip: text;
   -webkit-text-fill-color: transparent;
   
}
.modal_content {
  width: 38%;
  height:58vh;
  border-radius: 15px;
  background: rgba(58,40,106,1);
  position: relative;
  margin: 0 auto;
  margin-top: 40px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content:space-around;
  /* padding: 10px 0 10px 0; */
  /* box-shadow: 0px 4px 4px rgba(0, 0, 0, 0.25); */
}
.row_box {
  width: 26vw;
  height: 60px;
  /* display: flex; */
  flex-direction: row;
  align-items: center;
}

.modal-footer {
  display: flex;
  justify-content: center;
}

.btn-animate {
  text-decoration: none;
  color: #fff;
  font-size: 14px;
  font-weight: 600;
  letter-spacing: 2px;
  cursor: pointer;
  position: relative;
  z-index: 1;
  padding: 15px 30px;
  border: none;
  border-radius: 4px;
  box-shadow: 0px 16px 47px -15px #cda6ee;
  background: none;
  transition: all 0.2s cubic-bezier(0.19, 1, 0.22, 1);
  border-radius: 8px;
  overflow: hidden;
  outline: none;
  transform: translateZ(0);
}
.btn-animate span {
  position: relative;
  z-index: 2;
}
.btn-animate:before, .btn-animate:after {
  border-radius: 8px;
  content: "";
  z-index: -1;
  background: linear-gradient(100deg, #a6c1ee, #cc96eb);
  
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
.btn-animate:after {
  
  background: linear-gradient(100deg, #560a9b, #9e158f);
  transform: scaleY(0);
  transform-origin: top;
  transition: transform 0.5s cubic-bezier(0.19, 1, 0.22, 1);
  transition-delay: 0.1s;
}
.btn-animate:hover {
  box-shadow: 0px 16px 47px -15px  #cda6ee;
}
.btn-animate:hover:after {
  transform: scaleY(1);
  transform-origin: bottom;
  transition-delay: 0s;
}
.btn-animate:active {
  transform: translateY(4px) translateZ(0);
  box-shadow: 0px 8px 10px -6px  #cda6ee;
}

</style>
