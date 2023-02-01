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
import { inject } from 'vue'

export default {
    name: "ChatComp",
    setup() {
        // receive injected user details
        const userDetails = inject("providedDetails");

        return {
            userDetails
        }
    }
}
</script>

<template>
    <div>
        <h1>Chat Component</h1>
        <p>
            UserName: {{ userDetails.userName }} & UserId: {{ userDetails.userId }}
        </p>
    </div>
</template>