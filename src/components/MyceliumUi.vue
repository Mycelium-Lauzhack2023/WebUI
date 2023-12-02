<template>
    <b-container fluid>
        <b-row id="render-container" ref="twoContainer">
        </b-row>
        <b-row id="buttons-row">
            <b-button-group>
                <b-button>Create Zone</b-button>
                <b-button>Remove Zone</b-button>
                <b-button>Assign</b-button>
            </b-button-group>
        </b-row>
    </b-container>
</template>

<script>
    import Two from 'two.js';
    import { uuid } from 'vue-uuid';

    export default {
        name: 'MyceliumUi',
        data: () => {
            return {
                agents: []
            }
        },
        mounted() {
            // Create a new Two.js instance
            this.two = new Two({
                type: Two.Types.canvas,
                fitted: true,
                autostart: true
            });

            // Append the Two.js drawing to the DOM
            this.two.appendTo(this.$refs.twoContainer);

            // Create a spinning square
            this.agents.push(this.createNewAgent(50, 50));
            this.agents.push(this.createNewAgent(50,150));
            this.agents.push(this.createNewAgent(50,250));

            this.createNewZone(60,60,200,200);

            this.animate();
        },
        methods: {
            animate() {
                // test independent animations
                let agent1 = this.agents[0];
                let agent2 = this.agents[1];
                let agent3 = this.agents[2];

                this.updateAgentPosition(agent1.id, agent1.shape.position.x + 0.2, agent1.shape.position.y);
                this.updateAgentPosition(agent2.id, agent2.shape.position.x + 0.5, agent2.shape.position.y);
                this.updateAgentPosition(agent3.id, agent3.shape.position.x + 1, agent3.shape.position.y);

                // Update the rendering
                this.two.update();

                // Request the next animation frame
                requestAnimationFrame(this.animate);
            },
            createNewAgent(x, y) {
                const square = this.two.makeRectangle(x,y, 50, 50);
                square.fill = "#3498db";
                square.noStroke;
                this.two.update();

                return {
                    id: uuid.v4(),
                    shape: square
                };
            },
            createNewZone(x1, y1, x2, y2) {
                let w = x2 - x1;
                let h = y2 - y1;
                let x = w / 2;
                let y = h / 2;

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
        height: 90vh; /* 90% of the viewport height */
        background-color: #f0f0f0; /* Set a background color for the canvas */
    }

    /* Custom styles for the buttons row */
    #buttons-row {
      height: 10vh; /* 10% of the viewport height */
    }
</style>
