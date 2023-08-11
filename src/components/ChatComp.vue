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
	                stompClient.send(
	                    `/app/private`,
	                    JSON.stringify({ message: message.value, fromUserId: userDetails.userId, toUserId: recipientId.value, dateSent: new Date().toDateString() }),
	                    {}
	                );

					receivedMessages.value.push({ message: message.value, fromUserId: userDetails.userId, toUserId: recipientId.value, dateSent: new Date().toDateString() });
					message.value = "";
	            }
	        }

	        function connectWebSocket() {
	            socket = new SockJS("http://localhost:9101/ws-registry");
	            stompClient = Stomp.over(socket);
	            stompClient.connect(
	                (frame) => {
	                    console.log("Frame: " + frame);
	                       // TODO: confirm later on whether this is /user/queue/messages
	                    stompClient.subscribe(`/user/queue/user-${userDetails.userId}`, (message) => {
	                        receivedMessages.value.push(JSON.parse(message.body));
	                    });
	                },
	                (error) => {
	                    console.log(`An error occured: ${ error }`);
						stompClient.subscribe(`/queue/user-${userDetails.userId}`, (message) => {
							console.log(`Received message from: ${ message.body }`);
	                        receivedMessages.value.push(JSON.parse(message.body));
	                    });
	                }
	            )
	        }

	        return {
	            userDetails, sendMessage, message, receivedMessages, recipientId, connectWebSocket
	        }
	    },
	    mounted: function() {
	        alert("Refreshing the page destroys all your data");
	        this.connectWebSocket();
	    }
	}
</script>

<template>
	<div class="w-full h-full p-10 border-[1px] space-x-2">
		<div class="w-full h-full rounded-md border-[1px]">
			<div
				class="h-[10%] w-full flex justify-center items-center space-x-2 border-b-[1px]">
				<label for="to-user-id">Send Message To: </label>
				<input
					type="text"
					id="to-user-id"
					class="message-box w-[50%]"
					placeholder="Enter id of Recipient"
					v-model="recipientId" />
			</div>
			<div
				class="h-[80%] w-full overflow-y-scroll max-h-[90%] px-2 flex flex-col space-y-2">
				<div
					class="w-fit max-w-[85%] border rounded-full p-2"
					v-for="(message, index) in receivedMessages"
					:key="index"
					:class="
						message.fromUserId === userDetails.userId
							? 'self-end bg-green-300'
							: 'self-start bg-red-300'
					">
					<span>{{ message.message }}</span>
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
						:disabled="message === '' || recipientId === ''">
						<img
							src="src/assets/send_message.svg"
							alt="send-message"
							class="w-6" />
					</button>
				</div>
			</div>
		</div>
	</div>
</template>
