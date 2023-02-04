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
import { inject, ref, computed } from 'vue'
import SockJS from "sockjs-client/dist/sockjs"
import Stomp from "webstomp-client"

let socket;
let stompClient;

export default {
    name: "ChatComp",
    setup: function() {
        // receive injected user details
        const userDetails = inject("providedDetails");
        
        const message = ref("");
        const recipientId = ref("");
        const receivedMessages = ref([]);

        function sendMessage() {
            if(message.value !== "" && recipientId.value !== "") {
                console.log(`Sending message: ${ message.value } to: ${ recipientId.value }`);

                stompClient.send(
                    `/app/p2pmessage/${ recipientId.value }`, 
                    JSON.stringify({ message: message.value }),
                    {}
                );
            }
        }

        function uploadMedia() {

        }

        const processedMessages = computed(() => {
            return receivedMessages.value;
        });

        function connectWebSocket() {
            socket = new SockJS("http://localhost:8080/ws-entry");
            stompClient = Stomp.over(socket);
            stompClient.connect(
                {
                    username: userDetails.userName,
                    userid: userDetails.userId
                },
                (frame) => {
                    console.log("Frame: " + frame);
                    stompClient.subscribe("/user/queue/messages", (message) => {
                        receivedMessages.value.push(message.body);
                    });
                },
                (error) => {
                    console.log(`An error occured: ${ error }`);
                }
            )
        }

        return {
            userDetails, sendMessage, uploadMedia, message, receivedMessages, recipientId, connectWebSocket,
            processedMessages
        }
    },
    mounted: function() {
        alert("Refreshing the page destroys all your data");
        this.connectWebSocket();
    }
}
</script>

<template>
    <div class="w-full h-full p-10 flex border-[1px] space-x-2">
        <div class="flex flex-col w-1/3 h-full rounded-md border-[1px]">
            <div class="w-full flex flex-col items-center py-2 border-b-[1px]">
                <h1 class="text-center">Registered As:</h1>
                <div class="rounded-full bg-blue-400 w-20 h-20 flex justify-center items-center">
                    <span class="text-3xl">HK</span>
                </div>
            </div>
        </div>
        <div class="flex flex-col w-2/3 h-full rounded-md border-[1px]">
            <div class="h-[10%] w-full flex justify-center items-center space-x-2 border-b-[1px]">
                <label for="to-user-id">Send Message To: </label>
                <input 
                    type="text" 
                    id="to-user-id"
                    class="message-box w-[50%]"
                    placeholder="Enter id of Recipient"
                    v-model="recipientId" />
            </div>
            <div class="h-[80%] w-full overflow-y-scroll max-h-[90%]">
                <div
                    v-for="(message, index) in processedMessages" :key="index"
                    class="bg-red-500 h-8">
                    {{  message  }}
                </div>
            </div>
            <div class="h-[10%] w-full flex justify-around items-center px-2">
                <input 
                    type="text" 
                    id="message-box" 
                    class="message-box"
                    placeholder="Enter Message..."
                    v-model="message" />
                <div class="w-[20%] flex justify-center space-x-2">
                    <button 
                        class="action-button"
                        @click="sendMessage"
                        :disabled="message === '' || recipientId === '' ">
                        <img 
                            src="src/assets/send_message.svg" 
                            alt="send-message"
                            class="w-6" />
                    </button>
                    <button 
                        class="action-button"
                        @click="uploadMedia">
                        <img 
                            src="src/assets/upload_media.svg" 
                            alt="send-message"
                            class="w-6" />
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>