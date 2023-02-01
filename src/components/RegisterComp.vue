<!--
 Copyright 2023 Bikathi Martin
 
 Licensed under the Apache License, Version 2.0 (the "License");
 you may not use this file except in compliance with the License.
 You may obtain a copy of the License at
 
     http://www.apache.org/licenses/LICENSE-2.0
 
 Unless required by applicable law or agreed to in writing, software
 distributed under the License is distributed on an "AS IS" BASIS,
 WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 See the License for the specific language governing permissions and
 limitations under the License.
-->

<script lang="js">
import { ref } from 'vue'

export default {
    name: "RegisterComp",
    setup(props, context) {
        const userId = ref("");
        const userName = ref("");

        function startChat() {
            context.emit("start-chat", {
                userId: userId.value,
                userName: userName.value
            });
        }

        return {
            userId, userName, startChat
        }
    }
}
</script>

<template>
    <div class="w-full h-full flex justify-center items-center">
        <div class="border-[1px] w-[50%] h-[30%] border-black shadow-md rounded-sm">
            <form action="#" class="w-full h-full flex flex-col p-2 space-y-2">
                <div>
                    <h1 class="text-center text-xl">Enter Details to Get Started</h1>
                </div>
                <input 
                    type="text" 
                    id="user-id" 
                    class="input-style" 
                    placeholder="UserId e.g WS3424"
                    v-model="userId" />
                <input 
                    type="text" 
                    id="user-name" 
                    class="input-style" 
                    placeholder="UserName e.g cosmic_rabbit"
                    v-model="userName" />
                <button 
                    :disabled="userId === '' || userName === ''"
                    class="connect-button"
                    @click="startChat">Connect</button>
            </form>
        </div>
    </div>
</template>