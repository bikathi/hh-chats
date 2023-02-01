<script lang="js">
import RegisterComp from './components/RegisterComp.vue'
import ChatComp from './components/ChatComp.vue'
import { ref, reactive, provide } from 'vue'

export default {
  name: "App",
  components: {
    RegisterComp, ChatComp
  },
  setup() {
    const comp = ref("register-comp");
    const details = reactive({
      userName: "",
      userId: ""
    });

    // provide logged in user-details
    provide('providedDetails', details);

    function startChat(userDetails) {
      details.userName = userDetails.userName;
      details.userId = userDetails.userId;

      comp.value = "chat-comp";
    }

    return {
      comp, startChat, details
    }
  }
}
</script>

<template>
  <div class="w-screen h-screen">
    <component 
      :is="comp"
      @start-chat="startChat" />
  </div>
</template>