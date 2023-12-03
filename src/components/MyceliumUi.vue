<template>
    <div>
        <b-container fluid>
            <b-row id="render-container" ref="twoContainer">
            </b-row>
            <b-row id="buttons-row">
                <b-button-group>
                    <b-button>Create Zone</b-button>
                    <b-button>Remove Zone</b-button>
                    <b-button @click="showModal">Assign</b-button>
                </b-button-group>
            </b-row>
        </b-container>
        <b-modal hide-footer v-model="modalVisible" title="Zone Assignment">
            <b-list-group>
                <b-list-group-item v-for="agent in agents" :key="agent.name" @click="selectAgent(agent.id)">
                    {{ agent.name }} ----> {{ agent.zone }}
                </b-list-group-item> 
            </b-list-group>
            <b-button primary @click="hideModal">Navigate to zones</b-button>
        </b-modal>
    </div>
</template>

<script>
    import Two from 'two.js';
    import { uuid } from 'vue-uuid';
    import image from "../assets/map.png"
    import axios from 'axios'

    export default {
        name: 'MyceliumUi',
        data: () => {
            return {
                modalVisible: false,
                agents: [],
                solutions: {},
                solutionStep: 0,
                startNavigation: false
            }
        },
        async mounted() {
            // Create a new Two.js instance
            this.two = new Two({
                type: Two.Types.canvas,
                fitted: true,
                autostart: false,
                width: 400,
                height: 700
            });

            // Append the Two.js drawing to the DOM
            this.two.appendTo(this.$refs.twoContainer);

            // draw map image
            var texture = new Two.Texture(image);
            var sprite = new Two.Sprite(texture);
            sprite.translation.set(this.two.width / 2, this.two.height / 2);
            this.two.add(sprite);

            // get solutions from the service
            await this.getAgentPaths();

            // get agent initial positions
            let init1 = this.solutions.robot1[0];
            let init2 = this.solutions.robot2[0];
            let init3 = this.solutions.robot3[0];

            // create agents
            this.agents.push(this.createNewAgent(init1.x, init1.y, "Agent 1", "Zone A"));
            this.agents.push(this.createNewAgent(init2.x, init2.y, "Agent 2", "Zone B"));
            this.agents.push(this.createNewAgent(init3.x, init3.y, "Agent 3", "Zone B"));

            // create zones
            this.createNewZone(50,540,100,590);
            this.createNewZone(70,150,200,275);

            this.animate();

        },
        methods: {
            async getAgentPaths() {
                let response = await axios.get('http://127.0.0.1:7070/agents')
                let data = response.data;
                this.solutions.robot1 = data[0].coordinates;
                this.solutions.robot2 = data[1].coordinates;
                this.solutions.robot3 = data[2].coordinates;
            },
            animate() {
                // test independent animations
                let agent1 = this.agents[0];
                let agent2 = this.agents[1];
                let agent3 = this.agents[2];

                // easier to type
                let sol1 = this.solutions.robot1;
                let sol2 = this.solutions.robot2;
                let sol3 = this.solutions.robot3;

                if(sol1 && sol2 && sol3 && this.startNavigation) {
                    if(sol1.length > this.solutionStep) {
                        let step = sol1[this.solutionStep]
                        this.updateAgentPosition(agent1.id, step.x, step.y);
                    }
                    if(sol2.length > this.solutionStep) {
                        let step = sol2[this.solutionStep]
                        this.updateAgentPosition(agent2.id, step.x, step.y);
                    }
                    if(sol3.length > this.solutionStep) {
                        let step = sol3[this.solutionStep]
                        this.updateAgentPosition(agent3.id, step.x, step.y);
                    }

                    this.solutionStep = this.solutionStep + 1;
                }


                // Update the rendering
                this.two.update();

                // Request the next animation frame
                requestAnimationFrame(this.animate);
            },
            createNewAgent(x, y, name, zone) {
                const square = this.two.makeRectangle(x,y, 10, 10);
                square.fill = "#3498db";
                square.noStroke;
                this.two.update();

                return {
                    id: uuid.v4(),
                    shape: square,
                    name: name,
                    zone: zone
                };
            },
            createNewZone(x1, y1, x2, y2) {
                let w = x2 - x1;
                let h = y2 - y1;
                let x = x1 + (w / 2);
                let y = y1 + (h / 2);

                const square = this.two.makeRectangle(x,y, w, h);
                square.fill = "#b3046a";
                square.noStroke;
                square.opacity = 0.5;
                this.two.update();

                return {
                    id: uuid.v4(),
                    shape: square
                };
            },
            updateAgentPosition(uuid, x, y) {
                let agent = this.agents.find(agent => agent.id === uuid);
                if(agent) {
                    agent.shape.position.x = x;
                    agent.shape.position.y = y;
                }           
            },
            showModal() {
                this.modalVisible = true;
            },
            hideModal() {
                this.modalVisible = false;
                this.startNavigation = true;
            },
            selectAgent(agentId) {
                console.log(agentId);

            }
        },
        beforeDestroy() {
            // Remove the Two.js instance and clean up resources
            if (this.two) {
                this.two.clear();
            }
        },
    };
</script>

<style>
    #render-container {
        height: 700px;
        width: 400 px;
        background-color: #f0f0f0; /* Set a background color for the canvas */
    }

    /* Custom styles for the buttons row */
    #buttons-row {
        height: 10vh; /* 10% of the viewport height */
    }
</style>
